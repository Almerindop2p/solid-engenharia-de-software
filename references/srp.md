# SRP — Responsabilidade Única: guia de análise e aplicação

## Definição operacional

Um módulo deve responder a um **motivo principal de mudança**, entendido como uma responsabilidade coesa diante de um ator, requisito ou conjunto de regras relacionadas. **Responsabilidade não equivale a método, operação, camada ou arquivo.** Uma classe com vários métodos pode cumprir SRP; um método curto pode reunir responsabilidades indevidas.

Considere "ator" como a parte interessada ou origem de requisitos que demanda mudanças. Não é obrigatoriamente uma pessoa. Use evidências do projeto, não suposições sobre sua organização.

## Roteiro de engenharia reversa antes de refatorar

1. **Delimitar o pedido**: qual comportamento deve mudar e qual deve permanecer idêntico?
2. **Mapear o caminho real**: localizar ponto de entrada, chamadores, chamadas subsequentes, eventos, dependências, integrações e testes existentes. Não inventar arquivos, tabelas ou rotas.
3. **Identificar contratos observáveis**: entradas, saídas, erros, estados, persistência, efeitos assíncronos, logs relevantes, autenticação e comportamento de interface.
4. **Mapear responsabilidades e motivos de mudança**: para cada bloco relevante, indicar qual regra ele implementa e quem a faria mudar. Registrar conflitos reais, não apenas nomes ruins ou muitos métodos.
5. **Examinar coesão e acoplamento**: verificar quais partes mudam juntas, dependem das mesmas invariantes e poderiam evoluir independentemente. Considerar o custo das novas interfaces e dependências.
6. **Decidir a menor mudança segura**: manter, extrair função, extrair componente ou delimitar responsabilidade em módulo separado. Justificar separação somente se houver ganho concreto.
7. **Proteger comportamento anterior**: quando faltarem testes, caracterizar primeiro os casos atuais. Aplicar a alteração em passos pequenos, preservar contratos e comparar resultados.
8. **Relatar evidências**: explicar a responsabilidade encontrada, por que separou ou manteve, arquivos afetados, testes executados e limites de verificação.

## Diagnóstico: sinais, evidências e decisão

| Sinal observado | Verificação necessária | Orientação |
| --- | --- | --- |
| Regra fiscal e renderização de tela se alteram por demandas distintas | O componente implementa ambas diretamente? | Considerar extrair a regra de domínio, preservando o contrato da tela. |
| Métodos de consulta e gravação em um repositório | Ambos tratam da mesma persistência? | Em geral, manter: quantidade de métodos não viola SRP. |
| Serviço chama autenticação, validador e emissor | Apenas coordena responsabilidades delegadas? | Um orquestrador coeso pode ser adequado. |
| Módulo muda por regra fiscal, política de acesso e layout | Os três requisitos são independentes? | Se confirmado, separar limites de responsabilidade com interfaces existentes. |
| Arquivo extenso ou classe com muitos métodos | Existem motivos independentes de mudança? | Comprimento, por si só, não justifica refatorar. |
| Dependências cíclicas surgiriam com a extração | A separação aumenta o acoplamento? | Revisar a proposta e preferir a estrutura coesa mais simples. |

## Quando manter o código junto

- Os comportamentos protegem as mesmas invariantes e tendem a mudar pelo mesmo motivo.
- O componente é um coordenador que delega a execução de responsabilidades especializadas.
- A separação criaria apenas indireção, classes vazias, dependências artificiais ou duplicidade.
- A solicitação cabe em uma alteração pequena, sem motivo real para redesenhar o módulo.
- Faltam provas de responsabilidades independentes; nesse caso, não afirmar violação de SRP como fato.

## Quando considerar a separação

- Mudanças frequentes de uma regra causam ajustes ou regressões em outra regra independente.
- Existe lógica de domínio acoplada diretamente a detalhes de apresentação ou persistência, e essa mistura dificulta manutenção real.
- As partes possuem requisitos, invariantes, ciclos de mudança ou testes efetivamente independentes.
- A extração permite isolar efeitos colaterais, melhorar testes ou reduzir acoplamento total sem criar complexidade desproporcional.

## Exemplo prático: emissão de notas

**Cenário de possível violação:** uma classe `TelaEmissao` implementa diretamente regras de cálculo tributário, comunicação com o provedor fiscal, gestão de sessão e desenho dos componentes visuais. Uma alteração de layout afeta o mesmo módulo responsável por regras fiscais.

**Alternativa coesa:** manter a tela responsável pela interface; preservar ou extrair a regra fiscal para um componente do domínio; manter integração externa no adaptador ou cliente adequado; usar um caso de uso para coordenar a sequência quando a arquitetura já adotar esse padrão. **Não criar novas camadas automaticamente.**

**Exemplo que pode respeitar SRP:** um `EmissorNota` possui `prepararRequisicao`, `enviarNota` e `interpretarResposta`, todas operações necessárias à integração com o mesmo provedor. Ter três métodos não significa três motivos independentes de mudança.

## Critérios de aceitação e proteção

- [ ] A responsabilidade de cada componente afetado pode ser explicada em uma frase objetiva?
- [ ] Existe evidência de pelo menos dois motivos independentes de mudança quando se propõe uma separação?
- [ ] A extração melhora a coesão sem aumentar injustificadamente o acoplamento?
- [ ] O novo desenho segue as convenções do projeto e reutiliza componentes relevantes?
- [ ] Assinaturas, contratos públicos, persistência, erros, autorizações, estados e efeitos colaterais permaneceram compatíveis?
- [ ] Testes automatizados relevantes foram executados? Se não, quais verificações manuais ocorreram e o que segue não verificado?
- [ ] A alteração manteve o escopo solicitado e não adicionou funcionalidades especulativas?

**Regra final:** SRP orienta a organização de responsabilidades; não autoriza refatorar fora do pedido, reescrever fluxos estáveis ou separar tudo que puder ser separado.
