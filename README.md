# SOLID Engenharia de Software

Skill para orientar agentes de programação na análise, implementação, revisão, manutenção e refatoração de código, combinando os cinco princípios **SOLID** com **reutilização de código e DRY pragmático**.

**Autor:** Almerindo Junior

**Identificador:** `solid-engenharia-de-software`

O objetivo é fazer a menor alteração segura para atender a uma necessidade real, preservando a arquitetura, os contratos e os comportamentos existentes. O conteúdo é formado por instruções, referências técnicas, exemplos e checklists que o agente consulta durante o trabalho.

## Recursos e funcionalidades

| Recurso | Orientação fornecida |
| --- | --- |
| Análise do projeto | Inspecionar tecnologias, convenções, dependências, pontos de entrada e testes antes de editar. |
| Engenharia reversa | Rastrear consumidores, chamadas, contratos e efeitos colaterais para entender o comportamento real. |
| Aplicação de SOLID | Avaliar responsabilidades, extensibilidade, substituibilidade, interfaces e direção das dependências. |
| Busca antes da implementação | Procurar funções, métodos, serviços, hooks e utilitários existentes antes de criar novos. |
| Reutilização compatível | Conferir entradas, saídas, validações, erros, permissões e efeitos colaterais antes de reaproveitar código. |
| Refatoração incremental | Restringir mudanças ao escopo necessário e evitar reescrever fluxos estáveis. |
| Proteção de contratos | Preservar assinaturas, semântica, invariantes, autenticação, persistência e demais garantias aplicáveis. |
| Validação proporcional | Orientar testes de comportamento, contratos, consumidores e integrações conforme a alteração. |
| Relato técnico | Explicar decisões, arquivos afetados, verificações executadas e limitações restantes. |

Esses recursos são orientações para o agente: o pacote não contém um analisador estático, um refatorador automático ou um executor próprio de testes.

## Os cinco princípios na prática

| Princípio | Pergunta técnica | Aplicação |
| --- | --- | --- |
| **S — SRP: Responsabilidade Única** | Qual é o motivo principal de mudança deste componente? | Agrupar responsabilidades coesas e separar regras independentes quando houver benefício comprovado. |
| **O — OCP: Aberto/Fechado** | Onde acrescentar uma variante sem espalhar alterações? | Reutilizar estratégias, adaptadores, composição ou registros existentes; introduzir abstrações apenas para variações reais. |
| **L — LSP: Substituição de Liskov** | A implementação pode substituir a abstração sem romper suas garantias? | Preservar entradas válidas, pós-condições, invariantes, resultados, erros e efeitos contratados. |
| **I — ISP: Segregação de Interfaces** | De quais capacidades cada consumidor realmente precisa? | Preferir contratos coesos por papel, evitando operações artificiais e fragmentação excessiva. |
| **D — DIP: Inversão de Dependência** | Uma regra importante depende diretamente de um detalhe técnico instável? | Usar contratos adequados e conectar detalhes na borda da aplicação quando isso reduzir acoplamento real. |

O material diferencia **DIP** de **injeção de dependência (DI)** e não exige contêineres, interfaces por classe ou novas camadas por padrão. Também não considera tamanho de arquivo, quantidade de métodos ou presença de condicionais provas automáticas de um problema de projeto.

## Reutilização e DRY pragmático

A ordem de preferência é:

1. Chamar uma rotina existente que já cumpra o contrato necessário.
2. Compor funções existentes.
3. Fazer uma pequena extensão compatível.
4. Consolidar duplicação comprovadamente equivalente, dentro do escopo.
5. Criar uma nova implementação quando as alternativas anteriores não atenderem.

Economizar linhas significa reduzir código novo e custo de manutenção, preservando clareza, validação e tratamento de erros. Regras de negócio distintas devem continuar separadas mesmo quando sua sintaxe é parecida.

## Pontos fortes e benefícios esperados

- **Mudanças mais localizadas:** incentiva diffs pequenos e evita ampliar uma tarefa para uma reestruturação completa.
- **Menor duplicação:** prioriza implementações existentes e exige verificar compatibilidade antes de reutilizá-las.
- **Decisões fundamentadas:** pede evidências no código antes de declarar uma violação ou criar abstrações.
- **Atenção à compatibilidade:** considera os consumidores, os contratos públicos e os efeitos colaterais da alteração.
- **Melhor testabilidade:** orienta separar regras de detalhes externos quando o acoplamento dificulta testes ou manutenção.
- **Consulta por assunto:** distribui o aprofundamento em referências específicas para cada princípio.
- **Sem dependências de execução próprias:** o pacote é textual e não instala bibliotecas, serviços ou ferramentas no projeto analisado.

Os benefícios dependem da qualidade da análise, do agente utilizado e das validações disponíveis. Não há métricas de desempenho, redução de defeitos ou economia de linhas medidas neste repositório.

## Instalação no Codex

### Pré-requisitos

- Codex com suporte a skills locais.
- Git para as opções de clonagem abaixo, ou acesso ao download ZIP do repositório.
- Um projeto de código para analisar ou modificar.

A skill não exige Node.js, Python, Docker, banco de dados, chave de API própria ou etapa de build. O projeto em que ela será aplicada pode ter seus próprios requisitos.

### Opção 1 — Instalar para todos os seus projetos

No Linux, macOS ou Git Bash, execute:

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/Almerindop2p/solid-engenharia-de-software.git \
  "$HOME/.agents/skills/solid-engenharia-de-software"
```

No Windows com PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
git clone https://github.com/Almerindop2p/solid-engenharia-de-software.git `
  "$HOME/.agents/skills/solid-engenharia-de-software"
```

### Opção 2 — Instalar somente em um projeto

Na raiz do projeto que usará a skill:

```bash
mkdir -p .agents/skills
git clone https://github.com/Almerindop2p/solid-engenharia-de-software.git \
  .agents/skills/solid-engenharia-de-software
```

Essa opção cria um repositório Git dentro do projeto. Para distribuir a skill junto com o código da equipe sem um repositório aninhado, use a cópia dos arquivos descrita na próxima opção e versione-os no repositório principal.

### Opção 3 — Instalar manualmente, sem Git

1. Abra o [repositório no GitHub](https://github.com/Almerindop2p/solid-engenharia-de-software).
2. Selecione **Code → Download ZIP** e extraia o arquivo.
3. Crie uma pasta chamada `solid-engenharia-de-software` em `~/.agents/skills/` para uso pessoal ou em `.agents/skills/` na raiz do projeto para uso local.
4. Copie o conteúdo extraído para essa pasta, mantendo `SKILL.md`, `agents/` e `references/` juntos.
5. Confirme que o caminho final termina em `solid-engenharia-de-software/SKILL.md`, sem uma pasta adicional entre eles.

### Confirmar a instalação

O Codex detecta alterações em skills locais automaticamente. Se a skill não aparecer, reinicie o Codex. Na CLI ou extensão de IDE, use `/skills` ou digite `$` para localizar `solid-engenharia-de-software`; em interfaces com seletor `@`, procure pelo nome da skill. Esses caminhos e formas de ativação seguem a [documentação oficial de skills](https://learn.chatgpt.com/docs/build-skills).

Se houver entradas duplicadas, verifique se instalou a mesma skill nos escopos pessoal e do projeto. Caso ela continue indisponível, confira a localização do `SKILL.md` e se alguma configuração local desabilita a skill.

### Atualização

Se instalou por clonagem, atualize a instalação pessoal com:

```bash
git -C "$HOME/.agents/skills/solid-engenharia-de-software" pull --ff-only
```

Para a instalação por projeto, use o caminho `.agents/skills/solid-engenharia-de-software`. Se instalou por ZIP, baixe novamente e substitua os arquivos após preservar eventuais personalizações locais.

## Como usar

Depois de instalar, invoque a skill e descreva o objetivo concreto. Exemplos de prompts:

### Revisar código

```text
$solid-engenharia-de-software
Revise o módulo de pedidos. Identifique problemas de responsabilidade,
acoplamento e duplicação com evidências do código. Priorize os problemas
por impacto e proponha mudanças dentro do escopo desse módulo.
```

### Adicionar comportamento

```text
$solid-engenharia-de-software
Adicione suporte a um novo provedor de pagamento. Localize os contratos
e pontos de extensão existentes, preserve os provedores atuais e execute
os testes disponíveis para os fluxos afetados.
```

### Refatorar e reutilizar

```text
$solid-engenharia-de-software
Reduza a duplicação na validação de cadastro. Antes de extrair funções,
confirme que as regras são equivalentes. Preserve entradas, saídas,
mensagens de erro e permissões dos consumidores existentes.
```

Os cenários acima são sugestões de uso, não funcionalidades de negócio implementadas pelo pacote.

## Fluxo técnico de trabalho

1. **Inspecionar:** entender tecnologia, arquitetura, convenções, dependências e testes.
2. **Delimitar:** identificar a mudança solicitada e os comportamentos que devem permanecer.
3. **Mapear:** localizar responsabilidades, consumidores, contratos e pontos de extensão.
4. **Buscar reutilização:** comparar implementações existentes e seus efeitos observáveis.
5. **Implementar:** aplicar a menor mudança segura, seguindo os padrões do projeto.
6. **Validar:** executar os testes, verificações de tipos, lint ou checagens manuais pertinentes e disponíveis.
7. **Relatar:** informar decisões, reutilização realizada, alterações, verificações e riscos restantes.

## Estrutura e informações técnicas

```text
solid-engenharia-de-software/
├── README.md                   # Apresentação, instalação e uso
├── SKILL.md                    # Metadados e instruções principais
├── agents/
│   └── openai.yaml             # Nome e descrição para a interface do agente
└── references/
    ├── solid.md               # Visão consolidada dos cinco princípios
    ├── srp.md                 # Responsabilidades e motivos de mudança
    ├── ocp.md                 # Extensão de comportamentos
    ├── lsp.md                 # Substituibilidade e testes de contrato
    ├── isp.md                 # Contratos por capacidade e consumidor
    ├── dip.md                 # Direção de dependências e composição
    └── reutilizacao.md        # Busca, compatibilidade e DRY pragmático
```

| Característica | Detalhe |
| --- | --- |
| Tipo de pacote | Skill de instruções para agentes de programação. |
| Formato principal | Markdown com front matter YAML em `SKILL.md`. |
| Metadados da skill | Campos `name` e `description`. |
| Metadados de interface | `interface.display_name` e `interface.short_description` em `agents/openai.yaml`. |
| Idioma | Português brasileiro. |
| Exemplos técnicos | Trechos didáticos em TypeScript e cenários de manutenção de sistemas. |
| Linguagem do projeto analisado | As instruções não impõem linguagem ou framework; a aplicação depende das ferramentas do agente e da arquitetura encontrada. |
| Dependências declaradas | Sem pacotes de execução, servidores MCP ou ferramentas externas declarados pelo pacote. |
| Build e execução própria | Não há aplicação, API, servidor ou processo de build neste repositório. |
| Testes do pacote | Não há suíte automatizada incluída; os checklists orientam a validação no projeto em que a skill é aplicada. |

O agente consulta as instruções principais e as referências conforme a necessidade. Os exemplos fiscais e de integrações presentes nos guias são ilustrativos e não representam serviços implementados neste repositório.

## Referências internas

- [Instruções principais](SKILL.md)
- [Visão geral de SOLID](references/solid.md)
- [SRP — Responsabilidade Única](references/srp.md)
- [OCP — Aberto/Fechado](references/ocp.md)
- [LSP — Substituição de Liskov](references/lsp.md)
- [ISP — Segregação de Interfaces](references/isp.md)
- [DIP — Inversão de Dependência](references/dip.md)
- [Reutilização de código](references/reutilizacao.md)

## Contribuições

Ao propor melhorias, descreva o problema, a alteração e como verificou sua consistência. Preserve o foco em mudanças proporcionais, contratos honestos e exemplos claramente identificados como didáticos. Atualize os links e o README quando a estrutura ou as instruções mudarem.

## Autoria e licença

Criado por **Almerindo Junior**. O repositório ainda não inclui um arquivo de licença; consulte o autor sobre as condições de reutilização e distribuição.
