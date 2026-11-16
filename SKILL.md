---
name: solid-engenharia-de-software
description: Skill SOLID Engenharia de Software para aplicar boas práticas de engenharia de software em análise, implementação, revisão, manutenção e refatoração de código. Usar quando o usuário solicitar mudanças em projetos de software com preservação do comportamento existente, especialmente para aplicar os princípios SOLID de Responsabilidade Única (SRP), Aberto/Fechado (OCP), Substituição de Liskov (LSP), Segregação de Interfaces (ISP) e Inversão de Dependência (DIP), reduzir acoplamento, reutilizar métodos e funções existentes, minimizar linhas novas e evitar duplicação, funcionalidades ou abstrações desnecessárias.
---

# SOLID Engenharia de Software

**Autor:** Almerindo Junior

Aplicar mudanças pontuais e seguras, respeitando a arquitetura e os contratos existentes. Usar princípios como critérios de projeto, não como pretexto para reescrever código funcional.

## Fluxo de trabalho

1. Inspecionar os arquivos e identificar tecnologia, arquitetura, convenções, dependências, interfaces e testes relevantes.
2. Identificar o comportamento solicitado, os componentes responsáveis e as entradas e saídas afetadas.
3. Aplicar **SRP** para localizar a responsabilidade correta, **OCP** para avaliar pontos de extensão, **LSP** para verificar contratos das implementações substituíveis, **ISP** para limitar dependências às capacidades necessárias e **DIP** para proteger regras de alto nível de detalhes técnicos instáveis.
4. Antes de escrever métodos, funções, helpers ou novos arquivos, buscar implementações existentes e conferir se seus contratos e efeitos colaterais atendem ao caso. Preferir chamá-las a duplicá-las; minimizar linhas **novas**, sem comprimir o código ou prejudicar a clareza.
5. Alterar somente o necessário. Não inventar funcionalidades, contratos, dados ou dependências.
6. Criar ou atualizar testes para o cenário novo e para a compatibilidade com o comportamento anterior, quando possível.
7. Executar as validações disponíveis. Relatar o que foi alterado, o que foi testado e os riscos ou limitações restantes.

## Princípios em vigor

### S — Princípio da Responsabilidade Única (SRP)

- Dar a cada classe, módulo ou componente uma responsabilidade coesa e um motivo principal para mudar.
- Evitar agrupar indevidamente regras de negócio, persistência, validação e apresentação em um mesmo componente.
- Identificar a responsabilidade correta antes de alterar um arquivo.
- Separar responsabilidades apenas quando houver motivos de mudança independentes e benefício claro; não criar um arquivo por função automaticamente.
- Preservar contratos e funcionalidades existentes ao refatorar.

### O — Princípio Aberto/Fechado (OCP)

- Projetar pontos de variação para permitir **estender comportamentos** com o mínimo de mudanças em código estável.
- Antes de adicionar uma variação, procurar interfaces, estratégias, adaptadores, handlers, composição ou mecanismos de registro **já existentes** no projeto.
- Ao incluir um novo caso, preferir criar uma implementação compatível com o contrato existente, em vez de inserir múltiplas condições espalhadas pelo sistema.
- Preservar as entradas, saídas e regras dos casos existentes. Validar novos cenários e possíveis regressões.
- Introduzir novas abstrações **somente** quando existir uma variação real ou uma necessidade concreta; evitar antecipar extensões hipotéticas.
- Não interpretar OCP como proibição absoluta de editar arquivos existentes: correções, evolução de contratos e integrações podem exigir alterações localizadas e justificadas.
- Consultar [references/ocp.md](references/ocp.md) para engenharia reversa, decisões de extensão e critérios de validação.

### L - Princípio da Substituição de Liskov (LSP)

- Antes de criar ou trocar uma implementação, localizar o contrato da abstração e os consumidores que dependem dele.
- Garantir que o subtipo aceite as entradas válidas do contrato-base sem impor pré-condições mais restritivas.
- Preservar pós-condições, invariantes, significado dos resultados, erros previstos e efeitos colaterais contratados.
- Não supor substituibilidade apenas por uma interface compartilhada: verificar o comportamento efetivo e as capacidades de cada implementação.
- Quando os requisitos de entradas forem incompatíveis, preferir contratos especializados ou outra modelagem coerente com o projeto a forçar uma abstração falsa.
- Validar implementações intercambiáveis com os mesmos testes de contrato e testes dos consumidores, quando possível.
- Não criar herança, interfaces ou adaptadores apenas para alegar conformidade com LSP. Para exemplos e critérios, consultar [references/lsp.md](references/lsp.md).

### I — Princípio da Segregação de Interfaces (ISP)

- Fazer consumidores dependerem apenas das operações e capacidades de que realmente necessitam; considerar interfaces formais, tipos, APIs e protocolos efetivos.
- Mapear consumidores, implementadores, mocks e usos reais antes de dividir um contrato. Procurar evidências como métodos sem uso, `NotSupported` e obrigações artificiais.
- Preferir interfaces coesas por papel ou capacidade e a composição quando o projeto demonstrar esse benefício. Não criar uma interface por método automaticamente.
- Preservar assinaturas, contratos públicos, compatibilidade, erros e efeitos colaterais. Planejar migrações quando uma separação afetar consumidores existentes.
- Validar contratos e consumidores afetados com testes apropriados. Não interpretar um número elevado de métodos como violação automática.
- Consultar [references/isp.md](references/isp.md) para engenharia reversa, exemplo e critérios de aceitação.

### D — Princípio da Inversão de Dependência (DIP)

- Fazer regras de negócio e casos de uso dependerem de contratos que expressem suas necessidades, não diretamente de SDKs, bancos, rede ou detalhes de infraestrutura, quando houver benefício concreto.
- Antes de modificar dependências, rastrear consumidores, imports, implementações, pontos de composição e testes; reutilizar portas, adaptadores ou mecanismos de injeção existentes.
- Definir abstrações mínimas nos limites apropriados, sem vazar tipos técnicos ao alto nível. Fazer os detalhes concretos atenderem aos contratos; conectar implementações na borda da aplicação.
- Diferenciar **DIP** (direção do acoplamento) de **DI** (técnica de fornecer dependências). Não criar interfaces por classe nem instalar contêiner de DI por padrão.
- Preservar entradas, saídas, erros, autenticação, idempotência e efeitos colaterais contratados; testar regras de forma isolada e integrações reais quando possível.
- Não inverter dependências onde a abstração acrescentaria complexidade sem resolver um problema real. Consultar [references/dip.md](references/dip.md) para procedimento, exemplo e critérios.

## Reutilização de código e economia de linhas (DRY pragmático)

- **Buscar antes de criar:** localizar funções, métodos, utilitários, serviços, hooks, bibliotecas e pontos de extensão existentes que já resolvam a necessidade.
- **Reutilizar somente quando compatível:** verificar entradas, saídas, validação, regras de negócio, erros, efeitos colaterais, permissões e dependências. Semelhança de nomes não basta.
- **Priorizar a menor alteração segura:** preferir importar e chamar uma rotina existente a repetir sua implementação. Evitar wrappers, helpers, classes, interfaces, novos arquivos ou dependências que não tragam benefício concreto.
- **Reduzir repetição real:** ao encontrar lógica duplicada, considerar extraí-la ou consolidá-la apenas quando o comportamento for genuinamente equivalente e a mudança estiver no escopo. Não fundir regras de negócio distintas.
- **Otimizar o diff, não disputar menos linhas:** buscar menos código novo e menos manutenção futura, mas preservar nomes claros, tratamento de erros, validações, testes, segurança e desempenho. Não fazer *code golf* nem alterar arquivos fora do escopo para reduzir a contagem.
- **Comprovar:** revisar o diff e executar os testes viáveis; informar o que foi reutilizado, o que deixou de ser duplicado e quaisquer limitações de validação.
- Para o procedimento de busca, decisão e exemplos, consultar [references/reutilizacao.md](references/reutilizacao.md).

## Regras de proteção

- Não reestruturar todo o projeto para aplicar um princípio em uma mudança simples.
- Não alterar contratos públicos nem fluxos não relacionados sem necessidade demonstrada.
- Não duplicar regras de negócio ou caminhos de execução para evitar tocar em código existente.
- Não declarar que testes passaram sem executá-los.
- Em caso de conflitos, priorizar os requisitos explícitos do usuário e a compatibilidade com o sistema existente.

## Referências

- [references/solid.md](references/solid.md): visão consolidada dos cinco princípios SOLID (SRP, OCP, LSP, ISP e DIP).
- [references/srp.md](references/srp.md): engenharia reversa e critérios de responsabilidade única.
- [references/ocp.md](references/ocp.md): extensão localizada, contratos existentes e limites de abstração.
- [references/lsp.md](references/lsp.md): contratos observáveis, substituibilidade e testes de contrato.
- [references/isp.md](references/isp.md): dependências por consumidor, composição e preservação de contratos.
- [references/dip.md](references/dip.md): dependências de alto nível, abstrações, composição e critérios de validação.
- [references/reutilizacao.md](references/reutilizacao.md): reutilização de métodos e funções, DRY pragmático e economia de linhas com segurança.
