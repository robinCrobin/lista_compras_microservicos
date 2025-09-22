# 🛒 Sistema de Lista de Compras - Microsserviços

Sistema completo de microsserviços para gerenciamento de listas de compras usando NoSQL e arquitetura distribuída.

## 🏗️ Arquitetura

```
┌─────────────────┐    ┌──────────────────┐
│   Client Demo   │───▶│   API Gateway    │
└─────────────────┘    │   (Port 3000)    │
                       └─────────┬────────┘
                                 │
                    ┌────────────┼────────────┐
                    │            │            │
          ┌─────────▼────┐ ┌─────▼────┐ ┌────▼─────┐
          │ User Service │ │Item Service│ │List Service│
          │ (Port 3001)  │ │(Port 3003) │ │(Port 3002) │
          └──────────────┘ └────────────┘ └───────────┘
                    │            │            │
          ┌─────────▼────┐ ┌─────▼────┐ ┌────▼─────┐
          │   NoSQL DB   │ │  NoSQL DB │ │ NoSQL DB │
          │   (JSON)     │ │  (JSON)   │ │  (JSON)  │
          └──────────────┘ └───────────┘ └──────────┘
```

## 🚀 Como Executar

### Iniciar todos os serviços
```bash
./start-services.sh
```

### Parar todos os serviços
```bash
./stop-services.sh
```

### Executar demonstração completa
```bash
node client-demo.js
```

## 📝 Client Demo - Como Funciona

O `client-demo.js` é um cliente que demonstra todas as funcionalidades do sistema:

### 🔑 Autenticação
```javascript
// Registrar novo usuário
await client.register({
    email: 'usuario@exemplo.com',
    username: 'usuario123',
    password: 'senha123',
    firstName: 'Nome',
    lastName: 'Sobrenome'
});

// Fazer login
await client.login({
    identifier: 'usuario@exemplo.com', // email ou username
    password: 'senha123'
});
```

### 🏪 Gerenciar Itens
```javascript
// Buscar itens disponíveis
const items = await client.getItems();

// Buscar itens por categoria
const items = await client.getItems({ category: 'Alimentos' });

// Buscar itens por nome
const items = await client.getItems({ name: 'Arroz' });
```

### 📋 Gerenciar Listas
```javascript
// Criar nova lista
const lista = await client.createList({
    name: 'Lista do Supermercado',
    description: 'Compras da semana',
    status: 'active'
});

// Buscar listas do usuário
const listas = await client.getLists();

// Adicionar item à lista
await client.addItemToList(listaId, {
    itemId: 'item-uuid',
    itemName: 'Arroz',
    quantity: 2,
    unit: 'kg',
    estimatedPrice: 5.99
});
```

### 📊 Dashboard e Busca
```javascript
// Ver dashboard com estatísticas
const dashboard = await client.getDashboard();

// Busca global (listas + itens)
const resultados = await client.search('arroz');

// Verificar saúde dos serviços
await client.checkHealth();
```

## 🛣️ Rotas da API

### 🔐 Autenticação (`/api/auth`)
```bash
# Registrar usuário
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "teste@exemplo.com",
    "username": "teste123",
    "password": "senha123",
    "firstName": "Teste",
    "lastName": "Usuario"
  }'

# Login
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "identifier": "teste@exemplo.com",
    "password": "senha123"
  }'
```

### 👤 Usuários (`/api/users`)
```bash
# Listar usuários (requer token)
curl -X GET http://localhost:3000/api/users \
  -H "Authorization: Bearer SEU_TOKEN"

# Buscar usuário específico
curl -X GET http://localhost:3000/api/users/USER_ID \
  -H "Authorization: Bearer SEU_TOKEN"

# Atualizar perfil
curl -X PUT http://localhost:3000/api/users/USER_ID \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Novo Nome",
    "bio": "Nova bio"
  }'
```

### 🏪 Itens (`/api/items`)
```bash
# Listar todos os itens
curl -X GET http://localhost:3000/api/items

# Listar itens por categoria
curl -X GET "http://localhost:3000/api/items?category=Alimentos"

# Buscar item específico
curl -X GET http://localhost:3000/api/items/ITEM_ID

# Criar novo item (requer token)
curl -X POST http://localhost:3000/api/items \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Leite",
    "category": "Alimentos",
    "brand": "Marca X",
    "unit": "litro",
    "averagePrice": 4.50,
    "barcode": "1234567890",
    "description": "Leite integral"
  }'

# Listar categorias
curl -X GET http://localhost:3000/api/items/categories

# Buscar itens
curl -X GET "http://localhost:3000/api/search?q=arroz"
```

### 📋 Listas (`/api/lists`)
```bash
# Listar listas do usuário (requer token)
curl -X GET http://localhost:3000/api/lists \
  -H "Authorization: Bearer SEU_TOKEN"

# Criar nova lista (requer token)
curl -X POST http://localhost:3000/api/lists \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Lista do Supermercado",
    "description": "Compras da semana"
  }'

# Buscar lista específica
curl -X GET http://localhost:3000/api/lists/LIST_ID \
  -H "Authorization: Bearer SEU_TOKEN"

# Atualizar lista
curl -X PUT http://localhost:3000/api/lists/LIST_ID \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Novo Nome da Lista",
    "status": "completed"
  }'

# Deletar lista
curl -X DELETE http://localhost:3000/api/lists/LIST_ID \
  -H "Authorization: Bearer SEU_TOKEN"
```

### 🛒 Itens da Lista (`/api/lists/:id/items`)
```bash
# Adicionar item à lista
curl -X POST http://localhost:3000/api/lists/LIST_ID/items \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "itemId": "ITEM_ID",
    "itemName": "Arroz",
    "quantity": 2,
    "unit": "kg",
    "estimatedPrice": 5.99,
    "notes": "Arroz integral"
  }'

# Atualizar item na lista
curl -X PUT http://localhost:3000/api/lists/LIST_ID/items/ITEM_ID \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "quantity": 3,
    "purchased": true
  }'

# Remover item da lista
curl -X DELETE http://localhost:3000/api/lists/LIST_ID/items/ITEM_ID \
  -H "Authorization: Bearer SEU_TOKEN"

# Ver resumo da lista
curl -X GET http://localhost:3000/api/lists/LIST_ID/summary \
  -H "Authorization: Bearer SEU_TOKEN"
```

### 📊 Endpoints Agregados
```bash
# Dashboard com estatísticas (requer token)
curl -X GET http://localhost:3000/api/dashboard \
  -H "Authorization: Bearer SEU_TOKEN"

# Busca global
curl -X GET "http://localhost:3000/api/search?q=arroz"

# Status dos serviços
curl -X GET http://localhost:3000/health

# Registry de serviços
curl -X GET http://localhost:3000/registry
```

## 🗄️ Estrutura de Dados

### Usuário
```json
{
  "id": "uuid",
  "email": "usuario@exemplo.com",
  "username": "usuario123",
  "firstName": "Nome",
  "lastName": "Sobrenome",
  "password": "hash_bcrypt",
  "active": true,
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

### Item
```json
{
  "id": "uuid",
  "name": "Arroz",
  "category": "Alimentos",
  "brand": "Marca A",
  "unit": "kg",
  "averagePrice": 5.99,
  "barcode": "1234567890123",
  "description": "Arroz branco tipo 1",
  "active": true,
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

### Lista
```json
{
  "id": "uuid",
  "userId": "user-uuid",
  "name": "Lista do Supermercado",
  "description": "Compras da semana",
  "status": "active",
  "items": [
    {
      "itemId": "item-uuid",
      "itemName": "Arroz",
      "quantity": 2,
      "unit": "kg",
      "estimatedPrice": 5.99,
      "purchased": false,
      "notes": "",
      "addedAt": "2025-01-01T00:00:00.000Z"
    }
  ],
  "summary": {
    "totalItems": 1,
    "purchasedItems": 0,
    "estimatedTotal": 11.98
  },
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

## 🔧 Configuração

### Portas dos Serviços
- **API Gateway**: 3000
- **User Service**: 3001  
- **List Service**: 3002
- **Item Service**: 3003

### Bancos de Dados
Cada serviço tem seu próprio banco NoSQL (JSON):
- `service/user-service/database/users.json`
- `service/item-service/database/items.json`
- `service/list-service/database/lists.json`

### Service Registry
- Arquivo compartilhado: `shared/services-registry.json`
- Health checks automáticos a cada 30 segundos
- Circuit breaker: 3 falhas = circuito aberto

## 📋 Categorias de Itens Pré-cadastradas

- **Alimentos**: Arroz, Feijão, Macarrão, Açúcar
- **Limpeza**: Detergente, Sabão em Pó, Água Sanitária  
- **Higiene**: Shampoo, Condicionador, Sabonete
- **Bebidas**: Refrigerante, Suco de Laranja, Água Mineral
- **Padaria**: Pão Francês, Bolo de Chocolate, Croissant, Torta de Frango, Biscoito de Polvilho

## 🎯 Demonstração Automática

Execute `node client-demo.js` para ver uma demonstração completa que inclui:

1. ✅ Verificação de saúde dos serviços
2. ✅ Registro de novo usuário
3. ✅ Busca de itens disponíveis
4. ✅ Criação de lista de compras
5. ✅ Adição de 3 itens à lista
6. ✅ Visualização do dashboard agregado
7. ✅ Demonstração de todos os padrões de microsserviços

**Resultado esperado**: Lista criada com 3 itens e valor total estimado calculado automaticamente.