# Guia de Comandos de Migrações no Entity Framework Core (.NET 8)

Este artigo apresenta todos os comandos disponíveis para gerenciamento de migrações no **Entity Framework Core (EF Core)** para **.NET 8**, com base na documentação oficial da Microsoft. Os comandos são organizados por categoria, com descrições, exemplos e uma indicação dos mais importantes para o fluxo de desenvolvimento. Os comandos podem ser executados via **.NET Core CLI** (`dotnet ef`) ou **Package Manager Console (PMC)** no Visual Studio.

## Pré-requisitos

Para usar os comandos, instale o pacote `Microsoft.EntityFrameworkCore.Tools` no projeto:

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="8.*" />
```

Para a CLI, instale a ferramenta `dotnet-ef` globalmente:

```bash
dotnet tool install --global dotnet-ef --version 8.*
```

## Comandos de Gerenciamento de Migrações

Esses comandos criam, removem e inspecionam migrações.

### 1. Add-Migration / dotnet ef migrations add ★★★

**Descrição**: Cria uma nova migração com base nas alterações no modelo em relação ao snapshot anterior. Gera arquivos `.cs` e `.Designer.cs` na pasta `Migrations`.

**Sintaxe**:
- **PMC**: `Add-Migration <MigrationName> [-OutputDir <Path>] [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef migrations add <MigrationName> [--output-dir <Path>] [--context <DbContext>] [--project <Project>] [--startup-project <Project>]`

**Exemplo**:
```bash
dotnet ef migrations add InitialCreate
```
```powershell
Add-Migration InitialCreate
```

**Importância**: Essencial para capturar alterações no modelo (entidades ou `DbContext`).

### 2. Remove-Migration / dotnet ef migrations remove ★★

**Descrição**: Remove a última migração não aplicada, atualizando o snapshot. Não funciona se a migração já foi aplicada.

**Sintaxe**:
- **PMC**: `Remove-Migration [-Force] [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef migrations remove [--force] [--context <DbContext>] [--project <Project>] [--startup-project <Project>]`

**Exemplo**:
```bash
dotnet ef migrations remove
```
```powershell
Remove-Migration
```

**Importância**: Útil para corrigir migrações criadas incorretamente antes da aplicação.

### 3. Get-Migration / dotnet ef migrations list ★

**Descrição**: Lista todas as migrações, indicando quais foram aplicadas ao banco.

**Sintaxe**:
- **PMC**: `Get-Migration [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef migrations list [--context <DbContext>] [--project <Project>] [--startup-project <Project>] [--json]`

**Exemplo**:
```bash
dotnet ef migrations list
```
```powershell
Get-Migration
```

**Importância**: Ajuda a verificar o estado das migrações.

### 4. Check-Migration / dotnet ef migrations check

**Descrição**: Verifica se há alterações no modelo desde a última migração (novo no EF Core 8.0). Não gera arquivos, apenas informa.

**Sintaxe**:
- **PMC**: `Check-Migration [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef migrations check [--context <DbContext>] [--project <Project>] [--startup-project <Project>]`

**Exemplo**:
```bash
dotnet ef migrations check
```

**Importância**: Útil em pipelines CI/CD para validação.

## Comandos de Gerenciamento do Banco de Dados

Esses comandos interagem com o banco para aplicar ou reverter migrações.

### 1. Update-Database / dotnet ef database update ★★★

**Descrição**: Aplica migrações pendentes ao banco ou atualiza até uma migração específica. Cria o banco se ele não existir.

**Sintaxe**:
- **PMC**: `Update-Database [-Migration <MigrationName>] [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef database update [<MigrationName>] [--context <DbContext>] [--project <Project>] [--startup-project <Project>] [--connection <ConnectionString>]`

**Exemplo**:
```bash
dotnet ef database update
```
```powershell
Update-Database
```
- Reverter a uma migração:
```bash
dotnet ef database update InitialCreate
```

**Importância**: Fundamental para sincronizar o esquema do banco.

### 2. Drop-Database / dotnet ef database drop

**Descrição**: Exclui o banco associado ao `DbContext`. Útil em desenvolvimento.

**Sintaxe**:
- **PMC**: `Drop-Database [-Context <DbContext>] [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef database drop [--force] [--dry-run] [--context <DbContext>] [--project <Project>] [--startup-project <Project>]`

**Exemplo**:
```bash
dotnet ef database drop --force
```
```powershell
Drop-Database
```

**Importância**: Útil para reiniciar o esquema em testes.

## Comandos de Geração de Scripts

### 1. Script-Migration / dotnet ef migrations script ★★

**Descrição**: Gera um script SQL para aplicar migrações, cobrindo todas ou um intervalo. Suporta scripts idempotentes.

**Sintaxe**:
- **PMC**: `Script-Migration [-From <MigrationName>] [-To <MigrationName>] [-Idempotent] [-Output <File>] [-Context <DbContext>]`
- **CLI**: `dotnet ef migrations script [<From>] [<To>] [--idempotent] [--output <File>] [--context <DbContext>]`

**Exemplo**:
```bash
dotnet ef migrations script --idempotent --output migrations.sql
```
```powershell
Script-Migration -Idempotent -Output migrations.sql
```

**Importância**: Ideal para produção, permitindo revisão antes da aplicação.

## Comandos de Gerenciamento de DbContext

### 1. Scaffold-DbContext / dotnet ef dbcontext scaffold

**Descrição**: Gera classes de entidade e `DbContext` a partir de um banco existente (Database-First).

**Sintaxe**:
- **PMC**: `Scaffold-DbContext <ConnectionString> <Provider> [-OutputDir <Path>] [-Context <Name>] [-Force]`
- **CLI**: `dotnet ef dbcontext scaffold <ConnectionString> <Provider> [--output-dir <Path>] [--context <Name>] [--force]`

**Exemplo**:
```bash
dotnet ef dbcontext scaffold "Server=.;Database=MyDb;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

**Importância**: Usado em cenários Database-First.

### 2. DbContext-List / dotnet ef dbcontext list

**Descrição**: Lista todos os tipos `DbContext` no projeto.

**Sintaxe**:
- **PMC**: `DbContext-List [-Project <Project>] [-StartupProject <Project>]`
- **CLI**: `dotnet ef dbcontext list [--project <Project>] [--startup-project <Project>]`

**Exemplo**:
```bash
dotnet ef dbcontext list
```

**Importância**: Útil em projetos com múltiplos `DbContext`.

### 3. Use-DbContext (PMC)

**Descrição**: Define o `DbContext` padrão no PMC.

**Sintaxe**:
- **PMC**: `Use-DbContext <DbContext>`

**Exemplo**:
```powershell
Use-DbContext MyAppDbContext
```

**Importância**: Raro, usado com múltiplos `DbContext`.

## Comandos de Geração de Executáveis e Modelos

### 1. Bundle-Migration / dotnet ef migrations bundle

**Descrição**: Gera um executável (`efbundle.exe`) para aplicar migrações sem o .NET SDK.

**Sintaxe**:
- **PMC**: `Bundle-Migration [-Output <File>] [-Force] [-Context <DbContext>]`
- **CLI**: `dotnet ef migrations bundle [--output <File>] [--force] [--context <DbContext>]`

**Exemplo**:
```bash
dotnet ef migrations bundle --output efbundle.exe
```

**Importância**: Útil em deployments de produção.

### 2. Optimize-DbContext / dotnet ef dbcontext optimize

**Descrição**: Gera um modelo compilado para o `DbContext`, otimizando performance.

**Sintaxe**:
- **PMC**: `Optimize-DbContext [-OutputDir <Path>] [-Namespace <Namespace>] [-Context <DbContext>]`
- **CLI**: `dotnet ef dbcontext optimize [--output-dir <Path>] [--namespace <Namespace>] [--context <DbContext>]`

**Exemplo**:
```bash
dotnet ef dbcontext optimize
```

**Importância**: Usado em cenários de alta performance ou AOT.

## Comandos Mais Importantes

1. **Add-Migration** / `dotnet ef migrations add`: Cria migrações para refletir alterações no modelo.
2. **Update-Database** / `dotnet ef database update`: Sincroniza o banco com o modelo.
3. **Remove-Migration** / `dotnet ef migrations remove`: Corrige migrações antes da aplicação.
4. **Script-Migration** / `dotnet ef migrations script`: Gera scripts SQL para produção.

## Boas Práticas

- **Revise migrações**: Verifique os arquivos gerados por `Add-Migration` para evitar erros, como exclusão indesejada de colunas.
- **Use scripts em produção**: Prefira `Script-Migration --idempotent` para gerar scripts SQL revisáveis.
- **Verifique o estado**: Use `Get-Migration` ou `dotnet ef migrations list` antes de aplicar mudanças críticas.
- **Cuidado com reversões**: Migrações aplicadas requerem `Update-Database <PreviousMigration>` antes de `Remove-Migration`.

## Referências

- [Documentação Oficial do EF Core - Microsoft](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations)
- [Notas de Lançamento do EF Core 8.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0)