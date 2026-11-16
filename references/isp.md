# ISP - Princípio da Segregação de Interfaces

## Definição operacional

**Nenhum consumidor deve ser obrigado a depender de operações ou capacidades que não utiliza.** Projetar contratos coesos em torno das necessidades reais dos seus consumidores, sem forçar implementadores a fornecer métodos sem sentido para seu papel.

"Interface" inclui interfaces formais, tipos, protocolos, portas, APIs e outros contratos entre componentes. O ISP não obriga a criar uma interface para cada método e não proíbe um cliente que realmente usa várias capacidades de depender de todas elas.

## Procedimento de engenharia reversa

1. **Delimitar a alteração.** Identificar o caso de uso solicitado, o comportamento que deve permanecer e a camada afetada.
2. **Localizar o contrato existente.** Inspecionar interfaces/tipos, implementações, consumidores diretos e indiretos, mocks, adaptadores, testes e eventuais chamadas externas. Não inferir contratos apenas pelos nomes.
3. **Construir a matriz consumidor x operação.** Para cada consumidor, registrar quais métodos/capacidades ele chama, quais dados e erros espera e quais efeitos colaterais dependem do contrato.
4. **Procurar sinais verificáveis de interface excessiva.** Verificar métodos não utilizados por determinados consumidores, implementações com `throw new Error('Not supported')`, métodos vazios ou simulações que existem apenas para satisfazer uma assinatura.
5. **Distinguir problema real de variação legítima.** Uma interface ampla pode ser coesa quando todas as capacidades fazem parte do mesmo papel e os consumidores precisam delas; um método raramente chamado não é, por si só, violação.
6. **Comparar alternativas de menor custo.** Manter o contrato, expor uma visão menor do contrato existente, extrair interfaces por papel/capacidade, compor interfaces ou adaptar consumidores. Preferir padrões já utilizados no projeto; não inventar camadas ou abstrações.
7. **Analisar compatibilidade antes de alterar.** Verificar dependências públicas, assinaturas, injeção de dependências, implementação de mocks, consumidores externos, versões, integrações e o comportamento requerido pelo LSP.
8. **Aplicar mudanças incrementais e testar.** Cobrir cada consumidor alterado, implementações e contratos afetados, incluindo erros, autorização, estados e efeitos colaterais relevantes.
9. **Relatar a decisão.** Apresentar evidências da interface excessiva, alternativa escolhida, arquivos alterados, testes executados e riscos não verificados; se não houver evidência suficiente, manter a estrutura atual.

## Critérios para decidir

| Evidência no código | Pergunta de verificação | Direção sugerida |
| --- | --- | --- |
| Implementador tem métodos que sempre lançam `NotSupported` | O contrato exige capacidades que esse papel não oferece? | Avaliar contratos especializados por capacidade. |
| Um consumidor usa apenas consulta, mas recebe uma dependência com consulta, emissão e cancelamento | Ele precisa conhecer as operações adicionais? | Expor ao consumidor uma interface de leitura, se isso reduzir acoplamento real. |
| Muitos métodos ligados ao mesmo ciclo de vida e utilizados juntos | O conjunto expressa um papel coeso? | Manter junto, mesmo que a interface seja extensa. |
| Interfaces pequenas duplicam operações e geram conversões constantes | A divisão introduziu complexidade e acoplamento? | Preferir composição ou consolidar contratos coesos. |
| Mudança numa interface quebra mocks e implementações não relacionadas | A dependência foi ampliada sem necessidade? | Avaliar uma interface direcionada aos consumidores afetados. |

## Exemplo hipotético em TypeScript

Uma interface grande obriga todo implementador a fornecer capacidades que talvez não possua:

```ts
// Exemplo didático: os tipos são ilustrativos, não entidades já existentes no projeto.
interface OperacoesFiscais {
  emitirNfse(dados: DadosNfse): Promise<ResultadoEmissao>;
  emitirNfe(dados: DadosNfe): Promise<ResultadoEmissao>;
  consultarDas(cnpj: string): Promise<ResultadoDas>;
}
```

Se um serviço apenas consulta DAS, não deve precisar implementar ou receber os métodos de emissão. Quando o projeto demonstrar essa necessidade, modelar contratos por capacidade:

```ts
interface ConsultaDas {
  consultarDas(cnpj: string): Promise<ResultadoDas>;
}

interface EmissaoNfse {
  emitirNfse(dados: DadosNfse): Promise<ResultadoEmissao>;
}

interface EmissaoNfe {
  emitirNfe(dados: DadosNfe): Promise<ResultadoEmissao>;
}

// Um consumidor com duas necessidades pode depender da composição:
type ConsultaEEmissaoServico = ConsultaDas & EmissaoNfse;
```

**Ponto importante:** a segregação não declara NFS-e e NF-e substitutas entre si. Cada contrato possui entradas e regras próprias (LSP). Também não implica introduzir todas essas interfaces em um projeto real: primeiro confirmar as necessidades e os pontos de extensão existentes (SRP/OCP).

## Limites de aplicação

- Não criar uma interface por método, classe ou arquivo como regra automática.
- Não fracionar contratos públicos sem planejar migração e compatibilidade; quando necessário, usar adaptação ou depreciação gradual de acordo com o projeto.
- Não remover operações usadas por consumidores existentes.
- Não transferir para consumidores conhecimento de regras internas ou detalhes de infraestrutura apenas para reduzir o tamanho da interface.
- Em JavaScript ou sistemas sem interfaces formais, aplicar o mesmo princípio a objetos de dependência, APIs e protocolos efetivos. Não impor TypeScript ou padrões de outra linguagem.
- Não criar contratos artificiais para fluxos distintos apenas porque apresentam nomes semelhantes.
- Não ampliar o escopo da tarefa para reorganizar interfaces não relacionadas.

## Verificação e critérios de aceitação

- [ ] Consumidores e implementações reais foram mapeados, incluindo mocks e integrações relevantes?
- [ ] Cada consumidor depende somente das capacidades de que necessita, ou existe justificativa clara para uma interface coesa maior?
- [ ] A mudança elimina dependências desnecessárias verificadas, em vez de apenas multiplicar interfaces?
- [ ] As implementações não precisam fornecer métodos sem sentido, retornos falsos ou exceções inesperadas para satisfazer um contrato?
- [ ] Contratos de entradas, saídas, erros, autorização e efeitos colaterais foram preservados conforme SRP/OCP/LSP?
- [ ] Chamadas externas, contratos públicos e caminhos de compatibilidade foram tratados quando aplicável?
- [ ] Testes de consumidores, implementadores, mocks e contratos foram executados ou suas limitações foram documentadas?
- [ ] A solução respeita o escopo e as convenções existentes, sem novos arquivos ou abstrações especulativas?

**Regra final:** adaptar contratos às capacidades necessárias a cada cliente, preservando coesão, compatibilidade e simplicidade; não confundir segregação com fragmentação indiscriminada.
