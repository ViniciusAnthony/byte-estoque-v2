# Schema: schema1
Database: mysql
Tables: 14
Columns are NOT NULL unless marked nullable.

## empresas
id_empresa: int PK auto FK
razão social: varchar
cnpj: varchar unique
criado_em: date
IE: varchar
CEP: varchar

## usuarios
id_usuario: int PK auto FK unsigned
id_empresa: int
id_plano: int FK
nome: varchar
email: varchar unique
hash_senha: varchar unique
status: varchar

## planos
id_plano: varchar PK
tipo: varchar unique
preco: int unique

## assinatura
id_assinatura: int PK auto unsigned
id_empresa: int
id_plano: int FK
iniciada_em: date
encerrada_em: date
proxima_cobranca: date

## parceiros
id_parceiro: int PK auto FK unsigned
id_empresa: int
nome: varchar
documento: varchar unique
email: varchar unique
CEP: varchar
e_cliente: boolean
e_fornecedor: boolean
observacao: text

## interacoes
interacao_id: int PK auto unsigned
id_parceiro: int
id_usuario: int
ocorrencia: date
canal: varchar
descricao: text

## categorias
id_categoria: int PK auto FK unsigned
id_empresa: int
nome: varchar unique

## produtos
id_produto: int PK auto FK unsigned
id_categoria: int
sku: varchar
nome: varchar
descricao: varchar
unidade: int
preco: int

## operacoes
id_operacao: int PK auto FK unsigned
id_parceiro: int
id_usuario: int
tipo: varchar
status: varchar
ocorrencia: date
observacoes: text

## operacao_itens
id_operacao: int PK auto unsigned
id_produto: int PK
quantidade: int
valor_unitario: int

## titulos_financeiros
id_titulo: int PK auto FK unsigned
id_parceiro: int
tipo: varchar
descricao: varchar
valor: int
vencimento: date
cancelado: boolean

## baixas_financeiras
id_baixa: int PK auto unsigned
id_titulo: int
id_caixa: int
valor: int
realizada_em: date
forma_pagamento: varchar

## caixas
id_caixa: int PK auto FK unsigned
aberto_por: int
aberto_em: date
valor_abertura: int
fechado_em: date
valor_contado: int

## operacoes_caixa
movimento_id: int PK auto unsigned
id_caixa: int
id_usuario: int
tipo: varchar
valor: int
ocorrencia: date
descricao: text

## Relationships
usuarios.id_empresa → empresas.id_empresa (one-to-one)
planos.id_plano → usuarios.id_plano (one-to-one)
assinatura.id_empresa → empresas.id_empresa (one-to-one)
planos.id_plano → assinatura.id_plano (one-to-one)
parceiros.id_empresa → empresas.id_empresa (one-to-one)
interacoes.id_parceiro → parceiros.id_parceiro (one-to-one)
interacoes.id_usuario → usuarios.id_usuario (one-to-one)
categorias.id_empresa → empresas.id_empresa (one-to-one)
produtos.id_categoria → categorias.id_categoria (one-to-one)
operacoes.id_parceiro → parceiros.id_parceiro (one-to-one)
operacoes.id_usuario → usuarios.id_usuario (one-to-one)
operacao_itens.id_operacao → operacoes.id_operacao (one-to-one)
operacao_itens.id_produto → produtos.id_produto (one-to-one)
titulos_financeiros.id_parceiro → parceiros.id_parceiro (one-to-one)
baixas_financeiras.id_titulo → titulos_financeiros.id_titulo (one-to-one)
baixas_financeiras.id_caixa → caixas.id_caixa (one-to-one)
caixas.aberto_por → usuarios.id_usuario (one-to-one)
operacoes_caixa.id_caixa → caixas.id_caixa (one-to-one)
operacoes_caixa.id_usuario → usuarios.id_usuario (one-to-one)

---
If you suggest changes to this schema, ALSO return them as a DrawSQL patch: one fenced ```json code block, so I can apply them to my diagram. If you are only answering a question, skip the patch.

Patch rules:
- One JSON object with "strategy": "merge"; combine every change into that single patch.
- Reference tables and columns by name. Only include what changes — omit unchanged columns and unused optional fields.
- Column: {"name","type","length","is_primary_key","is_auto_increment","is_nullable","is_unique_key","is_index","is_unsigned","default","enum_values"} — use exact type names for mysql as shown in the schema above.
- Relationship: {"type":"one-to-many","source_table":"users","source_column":"id","target_table":"posts","target_column":"user_id"} — for "one-to-many" the source is the "one" side; for "many-to-one" the source is the "many" (foreign-key) side. Prefer "one-to-many".
- Add "default_type" ("string" | "function" | "number" | "boolean") when a default could be misread — a literal string "CURRENT_TIMESTAMP" or "0" needs "default_type":"string".
- SET columns (MySQL) list their members in "set_values", not "enum_values".
- Index: {"indexes":[{"name":"idx_posts_user_created","type":"index","columns":["user_id","created_at"]}]} on its table — "type" is "index" or "unique"; delete via "deletions".
- Group: {"name":"Billing"}. A group never lists its members — put "parent_name":"Billing" on each table that belongs to it.
- Sticky note: {"sticky_notes":[{"content":"..."}]}.
- Delete via "deletions"; rename via "old_name". Do not include left/top coordinates.

Examples:
Add table: `{"strategy":"merge","tables":[{"name":"posts","comment":"Blog posts","columns":[{"name":"id","type":"bigint","is_primary_key":true,"is_auto_increment":true},{"name":"user_id","type":"bigint","is_index":true},{"name":"title","type":"varchar","length":255},{"name":"body","type":"text","is_nullable":true},{"name":"created_at","type":"timestamp","is_nullable":true},{"name":"updated_at","type":"timestamp","is_nullable":true}]}]}`
Rename table and column (use old_name only when renaming): `{"strategy":"merge","tables":[{"name":"articles","old_name":"posts","columns":[{"name":"content","old_name":"body"}]}]}`
Delete: `{"strategy":"merge","deletions":{"tables":["tmp"],"columns":[{"table":"users","columns":["legacy"]}],"relationships":[{"source_table":"users","source_column":"id","target_table":"posts","target_column":"user_id"}]}}`
Multiple groups with nested tables (every table sets parent_name): `{"strategy":"merge","groups":[{"name":"Auth"},{"name":"Content"}],"tables":[{"name":"users","parent_name":"Auth","columns":[{"name":"id","type":"bigint","is_primary_key":true,"is_auto_increment":true}]},{"name":"roles","parent_name":"Auth","columns":[{"name":"id","type":"bigint","is_primary_key":true,"is_auto_increment":true}]},{"name":"posts","parent_name":"Content","columns":[{"name":"id","type":"bigint","is_primary_key":true,"is_auto_increment":true}]}]}`
