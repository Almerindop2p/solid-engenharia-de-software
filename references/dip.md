# DIP — Princípio da Inversão de Dependência

## Definição operacional

**Módulos de alto nível (regras e casos de uso) não devem depender diretamente de detalhes de baixo nível (bibliotecas, provedores, banco de dados, rede ou frameworks). Ambos devem depender de abstrações apropriadas. As abstrações não devem depender dos detalhes; os detalhes devem atender aos contratos das abstrações.**

Aplique esse princípio para proteger políticas importantes contra mudanças em infraestrutura ou integração, **quando houver dependência instável e benefício concreto**. O DIP trata da **direção do acoplamento**; injeção de dependência (DI) é uma técnica possível, não um sinônimo nem uma obrigação.

## Procedimento de engenharia reversa

1. **Delimitar o caso de uso.** Identificar a regra de negócio, o escopo solicitado e o comportamento que precisa permanecer estável.
2. **Mapear dependências existentes.** Localizar imports, instâncias criadas com `new`, funções globais, SDKs, acessos a banco, chamadas HTTP, gateways, filas, adaptadores, configurações, testes e o ponto onde os componentes são conectados.
3. **Localizar o motivo da mudança.** Verificar se uma regra de negócio exige trocar ou simular um detalhe técnico. Se o acoplamento não causa problemas reais, não introduzir uma abstração especulativa.
4. **Identificar contratos já disponíveis.** Priorizar portas, interfaces, funções injetadas, adapters, providers ou padrões de composição utilizados no projeto. Não duplicar contratos nem criar uma segunda arquitetura paralela.
5. **Definir uma abstração mínima quando necessária.** Descrever em termos do consumidor as operações e os dados necessários. Não vazar tipos proprietários de SDK, objetos HTTP ou detalhes de persistência para a regra de negócio, salvo exigência consciente e justificada do contrato.
6. **Mover a dependência concreta à borda.** Conectar o adaptador à abstração no ponto de composição existente (bootstrap, factory, provider ou configuração). Usar construtor, parâmetros, função ou mecanismo de DI já adotado. Não instalar um contêiner de DI apenas para aplicar DIP.
7. **Conferir o sentido das dependências.** Código de domínio/aplicação não deve precisar importar a implementação de infraestrutura para executar o caso de uso; adaptadores podem conhecer o contrato que atendem. Evitar ciclos de imports e dependências ocultas.
8. **Preservar os contratos observáveis.** Verificar argumentos, resultados, autenticação, autorização, tratamento de erros, timeouts, idempotência, persistência, logs e demais efeitos relevantes para o fluxo. Não mudar comportamento apenas para encaixar uma abstração.
9. **Validar e registrar.** Testar a regra com uma implementação substituta simples e testar separadamente a integração real quando viável. Relatar alterações, testes, decisões de design e limitações.

## Sinais para avaliar o DIP

| Sinal observado | Pergunta | Ação adequada |
| --- | --- | --- |
| Caso de uso importa diretamente SDK, cliente HTTP ou repositório concreto | A regra muda sempre que a tecnologia muda? | Avaliar contrato estável no limite da aplicação e adaptador de infraestrutura. |
| Teste da regra exige banco real ou rede sem necessidade funcional | Há uma dependência técnica que pode ser substituída? | Injetar a capacidade mínima necessária e testar o comportamento sem o detalhe externo. |
| Uma classe recebe diversos serviços e objetos globais | O problema é acoplamento real ou responsabilidades misturadas? | Aplicar SRP/ISP antes de adicionar interfaces indiscriminadas. |
| Já existe factory, contexto, provider ou porta equivalente | Uma abstração nova duplicaria a existente? | Reutilizar o padrão do projeto. |
| Componente simples e estável depende de utilitário interno | Existe variação real ou custo de manutenção? | Manter a dependência direta se a indireção só aumentar complexidade. |

## Exemplo ilustrativo em TypeScript

O exemplo é **didático**, não descreve classes nem regras existentes em ServeMEI ou em qualquer projeto real. Os nomes de operações e dados devem vir do código inspecionado, não deste modelo.

```ts
// Contrato voltado à necessidade do caso de uso.
type SituacaoCadastral = "ATIVA" | "INATIVA";

interface ConsultaSituacaoCadastral {
  consultar(cnpj: string): Promise<SituacaoCadastral>;
}

// Alto nível: conhece a capacidade, não o SDK da API.
class ObterSituacaoCadastral {
  constructor(private readonly consulta: ConsultaSituacaoCadastral) {}

  executar(cnpj: string): Promise<SituacaoCadastral> {
    return this.consulta.consultar(cnpj);
  }
}

// Detalhe concreto: adaptação de um cliente que já existe no projeto.
interface ClienteCadastralExterno {
  buscarSituacao(cnpj: string): Promise<SituacaoCadastral>;
}

class AdaptadorConsultaCadastral implements ConsultaSituacaoCadastral {
  constructor(private readonly cliente: ClienteCadastralExterno) {}

  consultar(cnpj: string): Promise<SituacaoCadastral> {
    return this.cliente.buscarSituacao(cnpj);
  }
}

// Composição na borda da aplicação: usando dependências existentes.
function criarCasoDeUso(cliente: ClienteCadastralExterno): ObterSituacaoCadastral {
  return new ObterSituacaoCadastral(new AdaptadorConsultaCadastral(cliente));
}
```

Em um teste, uma implementação em memória do contrato `ConsultaSituacaoCadastral` permite verificar o caso de uso sem acessar a rede. **Isso não substitui testes da integração real**, responsáveis por verificar mapeamento de dados, autenticação, erros e demais características externas.

A existência de uma interface **não garante o DIP** se o caso de uso continuar importando diretamente o SDK concreto ou instanciando o adaptador internamente. Da mesma forma, não exigir uma classe por implementação: funções e tipagem estrutural podem satisfazer o princípio quando forem adequadas à linguagem e ao projeto.

## Compatibilidade com os demais princípios

- **SRP:** deixar a regra no caso de uso e a comunicação técnica no adaptador quando forem responsabilidades distintas.
- **OCP:** permitir trocar uma integração por outro detalhe compatível, sem reescrever a política; não criar pontos de extensão hipotéticos.
- **LSP:** exigir que adaptações intercambiáveis respeitem garantias, erros e invariantes do contrato.
- **ISP:** expor ao consumidor somente as capacidades necessárias, sem interface excessivamente ampla.

## Limites e proibições

- Não obrigar toda classe, função, módulo ou utilitário a possuir uma interface.
- Não criar interfaces artificiais, camadas extras, fábricas ou contêiner de injeção de dependência sem ganho demonstrável.
- Não deslocar regras de negócio para adaptadores de infraestrutura apenas para inverter imports.
- Não fazer a abstração depender de tipos de SDK, banco ou framework sem justificativa arquitetural explícita.
- Não permitir referências circulares, instâncias escondidas ou service locators opacos para simular desacoplamento.
- Não substituir integrações efetivas por mocks no código de produção.
- Não alterar credenciais, configuração de segurança, políticas de retry, tratamento de erros nem contratos externos sem necessidade autorizada.
- Não refatorar módulos fora do escopo nem criar fluxos inexistentes no sistema real.

## Checklist de aceitação

- [ ] O caso de uso, sua regra e seus detalhes externos foram localizados no código real?
- [ ] Existe uma razão técnica verificável para inverter a dependência ou a estrutura atual já atende ao requisito?
- [ ] Contratos/pontos de composição existentes foram reutilizados quando adequados?
- [ ] O componente de alto nível depende apenas de capacidades necessárias, sem importar o adaptador concreto?
- [ ] Os detalhes técnicos implementam o contrato, e a composição acontece no limite apropriado?
- [ ] As abstrações evitam vazar tipos específicos de infraestrutura e preservam as garantias do contrato?
- [ ] O comportamento anterior, os efeitos colaterais, a segurança e as integrações foram preservados?
- [ ] Testes unitários/de contrato e testes de integração relevantes foram executados ou as limitações foram explicitadas?
- [ ] A solução evita novas abstrações sem necessidade, ciclos e ampliação de escopo?

**Regra final:** inverter a dependência somente onde isso protege comportamentos importantes de detalhes instáveis, seguindo a arquitetura comprovada do projeto e escolhendo a solução menos complexa que resolve o problema real.
