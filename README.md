# 🐳 Backup de um sistema Linux utilizando Docker

## 📖 Introdução

Este projeto demonstra como realizar o backup de um sistema Linux utilizando um container Docker, sem a necessidade de instalar ferramentas adicionais no sistema operacional.

A proposta é utilizar uma imagem Ubuntu temporária para executar o comando `tar`, acessando o sistema de arquivos do host em modo somente leitura e gerando um arquivo compactado contendo os dados do sistema.

Essa abordagem é útil para laboratórios, ambientes de desenvolvimento, máquinas pessoais e para compreender como o Docker pode ser utilizado em tarefas administrativas, além da execução de aplicações.

> **Atenção:** Este projeto possui fins educacionais e demonstra uma técnica de backup. Para ambientes de produção, recomenda-se utilizar soluções específicas de backup com suporte a versionamento, retenção, criptografia e restauração automatizada.

---

# ⚙️ Como funciona

O processo ocorre da seguinte forma:

1. Um container Ubuntu é iniciado temporariamente.
2. O sistema de arquivos do host é montado dentro do container em modo somente leitura (`Read Only`).
3. Uma pasta do host é compartilhada para armazenar o arquivo de backup.
4. O comando `tar` compacta os arquivos desejados.
5. O backup é salvo na pasta compartilhada.
6. Ao finalizar, o container é removido automaticamente.

Fluxo do processo:

```
Host Linux
     │
     ▼
Docker cria um container Ubuntu
     │
     ▼
Container acessa os arquivos do host (somente leitura)
     │
     ▼
tar compacta os arquivos
     │
     ▼
Backup salvo em ~/backup
     │
     ▼
Container removido automaticamente
```

---

# 📋 Pré-requisitos

Antes de iniciar, é necessário possuir:

* Linux
* Docker instalado
* Permissão para executar comandos Docker
* Espaço suficiente em disco para armazenar o backup

Verifique se o Docker está funcionando:

```bash
docker run hello-world
```

Caso a mensagem **"Hello from Docker!"** seja exibida, o ambiente está pronto.

---

# 🚀 Passo 1 – Criando a pasta de destino

Crie uma pasta onde o arquivo será armazenado.

```bash
mkdir -p ~/backup
```

---

# 🚀 Passo 2 – Executando o backup

Execute o comando abaixo:

```bash
docker run --rm \
-v /:/host:ro \
-v ~/backup:/backup \
ubuntu:24.04 \
bash -c "tar czpf /backup/backup-$(date +%F).tar.gz \
--exclude=/host/proc \
--exclude=/host/sys \
--exclude=/host/dev \
--exclude=/host/run \
--exclude=/host/tmp \
--exclude=/host/mnt \
--exclude=/host/media \
-C /host ."
```

### Explicação dos principais parâmetros

| Parâmetro             | Descrição                                                         |
| --------------------- | ----------------------------------------------------------------- |
| `--rm`                | Remove o container automaticamente ao finalizar                   |
| `-v /:/host:ro`       | Compartilha o sistema de arquivos do host em modo somente leitura |
| `-v ~/backup:/backup` | Compartilha a pasta onde o backup será salvo                      |
| `ubuntu:24.04`        | Imagem utilizada para executar o processo                         |
| `tar`                 | Responsável por criar o arquivo compactado                        |

---

# 🚀 Passo 3 – Verificando o backup

Após a conclusão, verifique se o arquivo foi criado.

```bash
ls -lh ~/backup
```

O resultado deverá ser semelhante a:

```text
backup-2026-07-31.tar.gz
```

Também é possível validar o conteúdo do arquivo sem extraí-lo:

```bash
tar -tzf ~/backup/backup-2026-07-31.tar.gz | head
```

---

# ✅ Resultado

Ao final do processo você terá um arquivo compactado contendo o backup do sistema.

Exemplo:

```
~/backup/

└── backup-2026-07-31.tar.gz
```

Esse arquivo pode ser armazenado em outro disco, servidor ou serviço de armazenamento em nuvem.

---

# 🔄 Restauração (opcional)

Para extrair o conteúdo do backup:

```bash
tar -xzpf backup-2026-07-31.tar.gz
```

> **Importante:** Restaurar um backup completo diretamente sobre um sistema operacional em uso pode sobrescrever arquivos existentes. Antes de realizar uma restauração em produção, valide o procedimento em um ambiente de testes e tenha um plano de recuperação.

---

# 📄 Licença

Este projeto é disponibilizado sob a licença MIT.

Sinta-se à vontade para estudar, adaptar e contribuir com melhorias.
