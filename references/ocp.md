# OCP — Princípio Aberto/Fechado

## Objetivo

Permitir acrescentar variantes de comportamento com alterações localizadas e mínimas no código estável, sem multiplicar condições, duplicar regras ou prejudicar os fluxos já existentes. **Aberto para extensão** não significa **proibido modificar**: ajustar um ponto de composição ou corrigir um defeito pode ser a solução adequada.

## Engenharia reversa antes da mudança

1. Localizar a regra solicitada, os consumidores, os pontos de entrada e os testes existentes.
2. Investigar como o projeto seleciona variantes: estratégias, interfaces, composição, configuração, roteadores, handlers, fábricas ou registros.
3. Identificar o que precisa variar e o que deve permanecer estável; verificar entradas, saídas, falhas, estados e efeitos colaterais.
4. Verificar se existe mecanismo de extensão útil **agora**. Não pressupor extensibilidade nem inventar estrutura ausente.
5. Comparar a reutilização de um ponto existente, uma alteração pontual simples e a criação de uma nova abstração. Escolher a alternativa menos complexa que satisfaça o requisito presente.

## Regras práticas

- Priorizar o reaproveitamento de métodos, funções e contratos existentes que atendam ao mesmo comportamento.
- Quando houver variação real, concentrar a seleção no lugar apropriado em vez de espalhar novos `if`/`switch` em vários módulos.
- Não criar hierarquias, registros de plug-ins, fábricas, interfaces ou wrappers para cenários futuros hipotéticos.
- Não compartilhar regras que apenas parecem semelhantes, mas possuem contratos ou semântica distintos.
- Permitir mudanças controladas no ponto de integração para registrar uma variante, quando necessário.
- Manter a definição dos contratos coerente com **LSP** e **ISP**; não forçar implementações incompatíveis sob uma interface genérica.
- Não alterar funcionalidades fora do escopo. Proteger autenticação, segurança, persistência, validações e tratamento de erros existentes.

## Exemplo conceitual

Se um sistema já possui uma seleção central de processadores por tipo, adicionar uma nova implementação ao mecanismo existente em vez de duplicar o mesmo fluxo de seleção por toda a aplicação. Se não existir mecanismo de seleção e for apenas um caso adicional simples, avaliar uma alteração localizada antes de introduzir uma arquitetura extensível inteira.

No contexto fiscal, NFS-e e NF-e podem ter capacidades e validações diferentes. Reutilizar o que for realmente comum, sem forçar intercambialidade entre emissores que não cumprem um contrato único.

## Checklist de aceitação

- [ ] Foram encontrados os pontos de extensão e os consumidores existentes?
- [ ] O comportamento já implementado foi mantido?
- [ ] A mudança é localizada e não espalha condicionais ou duplicação?
- [ ] As abstrações criadas têm necessidade atual comprovada?
- [ ] Foram respeitados os contratos de entradas, saídas, falhas e efeitos colaterais?
- [ ] Foram executados testes do cenário novo e dos fluxos existentes quando disponíveis?
- [ ] O resultado foi documentado com alterações e limitações verificadas, sem alegar testes não executados?
