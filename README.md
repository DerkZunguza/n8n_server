# N8N - Deploy na VPS (Contabo + Traefik + Portainer)

## Pré-requisitos na VPS

- [ ] Traefik rodando com entrypoint `websecure` e certresolver `letsencrypt`
- [ ] Network `traefik-public` criada: `docker network create traefik-public`
- [ ] Subdomínio DNS apontando para o IP da VPS: `n8n.eurekaplatformapi.xyz → 194.163.159.94`
- [ ] Portainer instalado e acessível

---

## Deploy via Portainer (Recomendado)

### 1. Configurar variáveis de ambiente

No Portainer, ao criar a Stack, adicionar as seguintes variáveis:

| Variável | Exemplo | Descrição |
|---|---|---|
| `N8N_SUBDOMAIN` | `n8n.eurekaplatformapi.xyz` | Subdomínio do N8N |
| `POSTGRES_PASSWORD` | `SenhaForte123!@#` | Senha do banco PostgreSQL |
| `N8N_ENCRYPTION_KEY` | `<string 64 chars>` | Chave de encriptação dos dados |

> Gerar N8N_ENCRYPTION_KEY: `openssl rand -hex 32`

### 2. Criar Stack no Portainer

```
Stacks > Add Stack
  Name: n8n
  Build method: Repository
  Repository URL: https://github.com/Helear0001/n8n_server
  Repository reference: refs/heads/master
  Compose path: docker-compose.yml
  Environment variables: (preencher conforme tabela acima)
Deploy!
```

Ou colar o conteúdo do `docker-compose.yml` diretamente no editor Web Editor.

---

## Deploy manual via SSH

```bash
# Na VPS
git clone https://github.com/Helear0001/n8n_server
cd n8n_server

# Configurar variáveis
cp .env.example .env
nano .env  # Preencher com valores reais

# Subir
docker compose up -d

# Ver logs
docker compose logs -f n8n
```

---

## Verificação pós-deploy

```bash
# Verificar containers rodando
docker ps | grep n8n

# Logs do N8N
docker logs n8n-app -f

# Logs do banco
docker logs n8n-postgres -f
```

Acesse: `https://n8n.eurekaplatformapi.xyz`

No primeiro acesso, o N8N pedirá para criar uma conta de administrador.

---

## Troubleshooting

### N8N não abre (504 Timeout)
```bash
# Verificar se o container está healthy
docker ps
docker logs n8n-app
```

### Erro de banco de dados
```bash
# Verificar se PostgreSQL está healthy
docker logs n8n-postgres
# O N8N aguarda o healthcheck do postgres automaticamente
```

### Resetar tudo (cuidado: apaga dados!)
```bash
docker compose down -v
docker compose up -d
```
