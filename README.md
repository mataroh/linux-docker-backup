# 🐳 Backup da pasta Home utilizando Docker

Realize o backup da sua pasta **Home** utilizando apenas um container Docker, sem instalar ferramentas adicionais no sistema operacional.

---

# 📖 Introdução

Este projeto demonstra como utilizar o Docker para executar um backup da pasta `/home` através do utilitário `tar`.

Ao invés de instalar programas de backup diretamente no sistema, utilizamos um container temporário apenas para executar a tarefa, mantendo o ambiente limpo e isolado.

Essa abordagem é ideal para:

* Backup dos arquivos pessoais
* Laboratórios
* Ambientes de desenvolvimento
* Aprender sobre volumes Docker
* Automatizar backups simples

---

# ⚙️ Como funciona

O Docker cria um container Ubuntu temporário.

Dentro dele, a pasta `/home` do computador é montada como somente leitura (`Read Only`).

Em seguida, o utilitário `tar` compacta todo o conteúdo da pasta Home em um único arquivo `.tar.gz`, armazenando-o em uma pasta compartilhada entre o host e o container.

Ao finalizar, o container é removido automaticamente.

Fluxo:

```text
/home
   │
   ▼
Docker
   │
   ▼
Container Ubuntu
   │
   ▼
tar
   │
   ▼
backup-home-2026-07-31.tar.gz
```

---

# 📋 Pré-requisitos

* Linux
* Docker instalado
* Permissão para executar o Docker

Verifique se está funcionando:

```bash
docker run hello-world
```

Se aparecer:

```text
Hello from Docker!
```

o ambiente está pronto.

---

# 🚀 Passo 1 — Criando a pasta de backup

Crie uma pasta onde será salvo o arquivo.

```bash
mkdir -p ~/backup
```

---

# 🚀 Passo 2 — Executando o backup

Execute o comando abaixo:

```bash
docker run --rm \
-v /home:/source:ro \
-v ~/backup:/backup \
ubuntu:24.04 \
bash -c "tar czpf /backup/backup-home-$(date +%F).tar.gz -C /source ."
```

### O que esse comando faz?

| Parâmetro             | Função                                              |
| --------------------- | --------------------------------------------------- |
| `--rm`                | Remove o container ao finalizar                     |
| `-v /home:/source:ro` | Compartilha a pasta `/home` em modo somente leitura |
| `-v ~/backup:/backup` | Pasta onde será salvo o backup                      |
| `ubuntu:24.04`        | Imagem utilizada                                    |
| `tar`                 | Compacta todos os arquivos da Home                  |

---

# 🚀 Passo 3 — Verificando o backup

Confira se o arquivo foi criado.

```bash
ls -lh ~/backup
```

Resultado esperado:

```text
backup-home-2026-07-31.tar.gz
```

---

# ✅ Validando o backup

Antes de armazenar o arquivo, verifique se ele pode ser lido.

```bash
tar -tzf ~/backup/backup-home-2026-07-31.tar.gz | head
```

Exemplo:

```text
./
./henrique/
./henrique/Documentos/
./henrique/Downloads/
./henrique/.bashrc
```

Se os diretórios forem listados, o backup foi criado corretamente.

---

# 📦 Estrutura do projeto

```text
docker-home-backup/
│
├── README.md
└── images/
    ├── hello-world.png
    ├── backup-running.png
    ├── backup-created.png
    └── validate-backup.png
```

---

# 🔄 Restaurando o backup

Crie uma pasta para restaurar os arquivos:

```bash
mkdir ~/restore
```

Extraia o conteúdo:

```bash
tar -xzpf ~/backup/backup-home-2026-07-31.tar.gz -C ~/restore
```

Todos os arquivos serão restaurados dentro da pasta `restore`, preservando a estrutura original da Home.

---

# 💡 Próximos passos

Algumas melhorias que podem ser implementadas neste projeto:

* Automatizar o backup com um script (`backup.sh`);
* Agendar execuções utilizando `cron`;
* Criptografar o arquivo gerado com GPG;
* Enviar o backup para um NAS ou armazenamento em nuvem;
* Adicionar logs da execução.

---

# 📄 Licença

Este projeto está licenciado sob a licença MIT.

Sinta-se à vontade para utilizar, modificar e compartilhar.
