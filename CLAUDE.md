# CLAUDE.md

Guia para o Claude Code trabalhar neste repositório. A fonte da verdade do produto é [`docs/spec.md`](docs/spec.md). Em caso de conflito, a spec vence. Se algo não estiver na spec, pergunte antes de inventar.

## O projeto

**TemHorarioAi** (logo: "Tem Horário Aí?") é um sistema de agendamento para profissionais de cílios e unhas, solo ou em estúdio. Cada estúdio e cada profissional tem um link público (para a bio do Instagram ou a descrição do WhatsApp) onde a cliente vê catálogo, portfólio, valores e horários livres e agenda sozinha. A profissional gerencia tudo por um painel PWA instalável no celular.

Estado atual: só especificação (`docs/spec.md`). Ainda não há código. Ao criar a base do projeto, atualize as seções "Comandos" e "Estrutura" abaixo.

## Decisões firmes (não reabrir sem pedido explícito)

### 1. Stack: Next.js + Supabase
- **Front-end e PWA:** Next.js com TypeScript (App Router).
- **Banco, login e arquivos:** Supabase: Postgres, Auth (Google e e-mail/senha) e Storage para fotos.
- Não usar Firebase nem outro backend.
- Hospedagem precisa permitir uso comercial no plano gratuito (candidato: Cloudflare). **Não usar o plano gratuito da Vercel** (não permite uso comercial). Evite APIs exclusivas de um provedor de hospedagem.
- Captcha: Cloudflare Turnstile (candidato). Notificações: Web Push (PWA). E-mail transacional: provedor a definir; isole o envio atrás de uma interface.

### 2. Sem API do WhatsApp
- Nada de API oficial, bibliotecas não oficiais ou mensagens automáticas por WhatsApp.
- Todo contato por WhatsApp é um **botão que abre o WhatsApp com mensagem pronta** (link `https://wa.me/<numero>?text=<mensagem codificada>`), clicado por uma pessoa.
- Avisos automáticos usam **Web Push** e **e-mail**.

### 3. Cliente sem conta
- A cliente nunca cria conta, senha ou instala app, e não há verificação (SMS, código etc.).
- Ela informa nome, WhatsApp (e-mail opcional), aceita a política de privacidade e passa pelo captcha.
- A cliente é identificada pelo **número de WhatsApp**, dentro de cada profissional (tabela `cliente` pertence à profissional).
- Depois de agendar, recebe um **link secreto da reserva** (código longo e aleatório) para ver status, cancelar, remarcar e confirmar presença.
- A cliente **nunca lê tabelas diretamente**. Todo acesso público passa por funções controladas no banco (RPC do Postgres / `security definer`) que devolvem só horários livres e a própria reserva.

### 4. Modo de agendamento por profissional: automático ou com aprovação
- Configuração da **profissional** (não do estúdio): `automatico` ou `com_aprovacao`.
- **Automático:** o agendamento nasce `confirmado`.
- **Com aprovação:** nasce `pendente` e **segura o horário** até a profissional aprovar ou recusar. Só vira definitivo depois da aprovação.
- Remarcar no modo com aprovação volta o agendamento para `pendente`.
- Status possíveis: `pendente`, `confirmado`, `cancelado`, `concluido`, `falta`.

### 5. Solo é um estúdio de uma pessoa
- Existe **uma só lógica**: todo profissional pertence a um estúdio. A profissional solo é dona de um estúdio com uma única profissional.
- Não crie caminhos de código, tabelas ou telas separados para "solo".
- Links por caminho no mesmo domínio: `/{estudio}` e `/{estudio}/{profissional}`. Em estúdio com uma profissional, `/{estudio}` leva direto aos serviços dela.

## Regras de negócio que não podem quebrar

- **Sem sobreposição:** nunca dois agendamentos ativos (`pendente` ou `confirmado`) sobrepostos para a mesma profissional, nem com requisições simultâneas. Garantir **no banco** com restrição de exclusão (`EXCLUDE USING gist` sobre `tstzrange`, por profissional, filtrando os status ativos). Validação só na aplicação não basta. Se o banco recusar, a cliente escolhe outro horário.
- **Disponibilidade** (RF05): horário semanal com pausas, bloqueios, agendamentos existentes, intervalo entre atendimentos, antecedência mínima e limite máximo. Remarcar passa pela mesma verificação.
- **Vários serviços no mesmo agendamento:** só com a mesma profissional e só se ela liberar e escolher quais podem ser combinados.
- **Item do agendamento** grava serviço, opções, preço e duração no momento da reserva (preserva histórico mesmo se o catálogo mudar).
- **Manutenção:** o sistema sugere a faixa de preço pela última aplicação concluída com aquela profissional. O valor aparece como **estimado** até a profissional confirmar.
- **Prazo de cancelamento/remarcação** é por profissional. Passado o prazo, o link da reserva mostra só o botão de WhatsApp.
- **Compartilhamento com a dona** é por profissional: `desligado` (padrão), `so_ver` ou `ver_e_editar`. A dona não vê a agenda de quem não liberou.
- Conta suspensa: página pública mostra "indisponível no momento".
- Falha no envio de e-mail **não bloqueia** o agendamento.
- Antiabuso: captcha invisível, limite de agendamentos futuros simultâneos por WhatsApp e limites por IP.

## Segurança e LGPD

- **RLS ligado em todas as tabelas.** Cada profissional só acessa os próprios dados; a dona só o que foi liberado; o administrador da plataforma tem acesso próprio.
- Nunca expor a `service_role` key no cliente. Chave anônima só para chamar as funções públicas.
- Fotos públicas apenas do portfólio e dos serviços. Fotos são reduzidas e convertidas para WebP no envio.
- Slugs reservados (`admin`, `api`, `reserva`, `login` e semelhantes) não podem ser usados por estúdios ou profissionais.
- Consentimento no agendamento, política de privacidade, termos de uso e exclusão de dados mediante pedido.

## Convenções

- **Idioma:** interface e textos em português do Brasil. Nomes do domínio podem ficar em português (`estudio`, `profissional`, `agendamento`, `servico`), sem acentos em identificadores de código e banco.
- **Fuso horário único:** `America/Sao_Paulo`. Gravar instantes como `timestamptz` e converter só na exibição.
- **Mobile first:** a página pública precisa carregar rápido em rede móvel. Acessibilidade básica (contraste, área de toque, rótulos).
- **Custo próximo de zero:** prefira recursos dos planos gratuitos. Plano gratuito do Supabase: 500 MB de banco, 1 GB de arquivos e pausa por inatividade.
- **Mudanças de banco** sempre por migrations versionadas do Supabase, nunca editando o banco à mão.
- **Escopo:** implemente só o que está na v1 da spec. Itens da fase 2 (Google Agenda, sinal por Pix, temas, recepcionista, cobrança automática, app nativo etc.) ficam fora, e nada entra pela metade. Na v1 não há pagamento no sistema; a cobrança das profissionais é manual (Pix por fora), com ativação pelo administrador.

## Testes prioritários

1. Cálculo de horários livres (horário semanal, bloqueios, intervalos, limites, vários serviços).
2. Agendamentos simultâneos para o mesmo horário (o banco recusa o segundo).
3. Regras de acesso (RLS) entre profissionais, dona e administrador.
4. Ponta a ponta: agendar, aprovar, cancelar, remarcar, confirmar presença.
5. Celulares reais (iPhone e Android), incluindo instalação do PWA e push.

## Decisões em aberto

Veja a seção 14 de `docs/spec.md` (provedor de e-mail, hospedagem final, período de teste, limites de abuso, slugs reservados, faixas de manutenção etc.). Não decida esses pontos sozinho: deixe configurável ou pergunte.

## Comandos

_A definir quando o projeto Next.js for criado._

## Estrutura

_A definir quando o projeto Next.js for criado._
