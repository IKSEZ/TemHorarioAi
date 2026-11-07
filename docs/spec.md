# TemHorarioAi: Especificação v0.1

> Documento de especificação inicial do **TemHorarioAi** (logo: "Tem Horário Aí?"), criado em 07/11/2026 a partir das decisões tomadas em conversa. Itens ainda em aberto estão na seção "Decisões em aberto".

## 1. Visão geral

Sistema de agendamento para profissionais de cílios e unhas (solo ou em estúdio) que hoje usam vários apps separados para anotar pedidos, marcar horários e enviar catálogo. Cada profissional ou estúdio tem **um link único** para colocar na bio do Instagram ou na descrição do WhatsApp. Por esse link, a cliente vê catálogo, portfólio, valores e horários disponíveis e agenda sozinha, **sem criar conta e sem instalar nada**.

A profissional gerencia tudo por um painel instalável no celular (PWA).

## 2. Princípios

- A cliente nunca precisa de conta, senha ou app.
- Custo de infraestrutura mínimo no início, com estrutura pronta para crescer.
- Sem API do WhatsApp (decisão firme). O contato é feito por botão que abre o WhatsApp com mensagem pronta.
- Solo é um estúdio com uma única profissional, para evitar duas lógicas diferentes.
- Tudo que for funcionalidade entra funcionando corretamente. O que ficar para depois fica fora da v1, sem entrar pela metade.

## 3. Usuários e papéis

- **Cliente:** acessa o link público, agenda, cancela, remarca e confirma presença pelo link da reserva. Não tem conta.
- **Profissional:** tem conta (Google ou e-mail e senha). Gerencia serviços, agenda, clientes e relatórios. Pode pertencer a um estúdio.
- **Dona do estúdio:** convida e remove profissionais e edita a página do estúdio. Só vê ou edita a agenda de uma profissional se ela liberar.
- **Administrador da plataforma:** acesso exclusivo do dono do sistema. Ativa, suspende e consulta contas.

## 4. Escopo

### Versão 1

- Link público por estúdio e por profissional, no mesmo domínio.
- Catálogo com serviços, variações, adicionais e preço de manutenção por faixa de dias.
- Portfólio com fotos.
- Agenda configurável por profissional.
- Agendamento no modo automático ou com aprovação.
- Vários serviços no mesmo agendamento com a mesma profissional, quando liberado.
- Link da reserva: cancelar, remarcar e confirmar presença.
- Painel PWA com notificações push, e-mail e botão de WhatsApp com mensagem pronta.
- Lista de clientes com histórico, anotações e lembrete de manutenção.
- Relatórios simples.
- Estúdios com várias profissionais e compartilhamento opcional com a dona.
- Painel de administrador com cobrança manual.
- Proteção contra abuso e adequação à LGPD.

### Fase 2

- Sincronização com Google Agenda (primeiro só envio dos agendamentos; depois nos dois sentidos).
- Sinal por Pix, configurável por profissional ou estúdio.
- Tema e cores da página pública.
- Avisos fixos no topo da página.
- Fotos de antes e depois por cliente (uso privado).
- Controle de faltas e lista de clientes bloqueadas.
- Pacotes e combos, venda de produtos.
- Serviços combinados entre profissionais diferentes.
- Papel de recepcionista.
- Domínio próprio da profissional.
- Cobrança automática de assinatura.
- Liberação automática de horário não confirmado.
- App nativo nas lojas.

### Fora do escopo

- API oficial do WhatsApp e mensagens automáticas por WhatsApp.
- Contas ou login para clientes.

## 5. Requisitos funcionais

### 5.1 Página pública

- **RF01:** cada estúdio tem um link único (`/{estudio}`) e cada profissional tem um link dentro dele (`/{estudio}/{profissional}`). Em estúdio solo, o link do estúdio leva direto aos serviços da profissional.
- **RF02:** a página mostra nome, foto ou logo, bio, endereço com link para o mapa, links de Instagram e WhatsApp, catálogo e portfólio.
- **RF03:** em estúdio com várias profissionais, a cliente escolhe uma profissional ou "qualquer uma disponível".
- **RF04:** a cliente escolhe serviço, variações e adicionais, e vê preço e duração totais.
- **RF05:** os horários livres são calculados a partir do horário semanal, bloqueios, agendamentos existentes, intervalo entre atendimentos, antecedência mínima e limite máximo.
- **RF06:** a cliente informa nome e WhatsApp (e-mail opcional), aceita a política de privacidade e passa pelo captcha.
- **RF07:** conta suspensa mostra "indisponível no momento" na página pública.

### 5.2 Catálogo e portfólio

- **RF10:** serviço com nome, descrição, duração, preço, fotos, ordem e status ativo.
- **RF11:** variações e adicionais por serviço, somando preço e duração.
- **RF12:** serviços de manutenção com faixas de dias desde a última aplicação, cada faixa com preço e duração.
- **RF13:** o sistema reconhece a cliente pelo número de WhatsApp, acha a última aplicação concluída com aquela profissional e **sugere** a faixa. O valor é exibido como estimado até a profissional confirmar.
- **RF14:** portfólio com fotos e legendas, ligadas ou não a um serviço. As fotos são reduzidas e convertidas para WebP no envio.

### 5.3 Agenda

- **RF20:** horário semanal por profissional, com pausas.
- **RF21:** bloqueios pontuais (folgas, feriados, compromissos).
- **RF22:** intervalo automático entre atendimentos.
- **RF23:** antecedência mínima e limite máximo para agendar.
- **RF24:** modo de agendamento por profissional: **automático** (confirma na hora) ou **com aprovação** (fica pendente e segura o horário até aprovar ou recusar).
- **RF25:** opção por profissional para permitir vários serviços no mesmo agendamento, escolhendo quais podem ser combinados. Só com a mesma profissional.
- **RF26:** prazo de cancelamento e remarcação definido por profissional.

### 5.4 Link da reserva (cliente)

- **RF30:** após agendar, a cliente recebe um link pessoal e secreto da reserva.
- **RF31:** por esse link ela vê o status, cancela ou remarca até o prazo, e confirma presença.
- **RF32:** passado o prazo, aparece apenas o botão de WhatsApp.
- **RF33:** remarcar passa pela mesma verificação de disponibilidade. No modo com aprovação, a remarcação volta a ficar pendente.

### 5.5 Painel da profissional (PWA)

- **RF40:** login com Google ou e-mail e senha, com recuperação de senha.
- **RF41:** agenda do dia e da semana, com novos agendamentos em destaque.
- **RF42:** aprovar, recusar, cancelar, remarcar e marcar como concluído ou falta.
- **RF43:** notificações push (PWA), e-mail para novo agendamento, cancelamento e remarcação.
- **RF44:** botão que abre o WhatsApp com mensagem pronta (confirmação, lembrete, retorno).
- **RF45:** confirmação de presença leve: um dia antes, o painel destaca as não confirmadas e a profissional decide se libera o horário.
- **RF46:** lista de clientes com histórico (data, serviço, valor) e anotações.
- **RF47:** lembrete de manutenção: destaca clientes perto de passar do prazo, com mensagem pronta de WhatsApp.
- **RF48:** relatórios simples: faturamento do mês, serviços mais pedidos, horários mais cheios.

### 5.6 Estúdio

- **RF50:** a dona convida e remove profissionais e edita a página do estúdio.
- **RF51:** cada profissional controla seus serviços, preços, agenda e clientes.
- **RF52:** cada profissional escolhe o compartilhamento com a dona: **desligado** (padrão), **só ver** ou **ver e editar**.

### 5.7 Administração da plataforma

- **RF60:** painel exclusivo do administrador para listar contas, ativar, suspender e definir vencimento e período de teste.
- **RF61:** cobrança **manual**, por fora do sistema (Pix). A estrutura de plano, status e vencimento já existe para permitir cobrança automática depois.

### 5.8 Antiabuso

- **RF70:** captcha invisível no agendamento.
- **RF71:** limite de agendamentos futuros simultâneos por número de WhatsApp.
- **RF72:** no modo com aprovação, o horário só vira definitivo depois da aprovação.

## 6. Requisitos não funcionais

- **RNF01:** mobile first. A página pública deve carregar rápido em rede móvel.
- **RNF02:** PWA instalável, com notificações push.
- **RNF03:** nunca permitir dois agendamentos ativos sobrepostos para a mesma profissional, nem em requisições simultâneas (garantido no banco).
- **RNF04:** fuso horário único: America/Sao_Paulo. Idioma: português do Brasil.
- **RNF05:** falha no envio de e-mail não bloqueia o agendamento.
- **RNF06:** custo de infraestrutura próximo de zero no início.
- **RNF07:** acessibilidade básica (contraste, tamanho de toque, rótulos).

## 7. Modelo de dados

- **estúdio:** nome, slug único global, bio, logo, endereço, WhatsApp, Instagram, status do plano, vencimento, fim do teste.
- **profissional:** estúdio, usuário, slug único dentro do estúdio, bio, foto, configurações (modo de agendamento, vários serviços, intervalo, antecedência, limite, prazo de cancelamento, compartilhamento com a dona).
- **papel:** dona ou profissional, por estúdio.
- **serviço:** profissional, nome, descrição, duração, preço, ordem, ativo.
- **opção de serviço:** variação ou adicional, com acréscimo de preço e duração.
- **faixa de manutenção:** serviço, de/até (dias), preço, duração.
- **item de portfólio:** profissional, imagem, legenda, serviço opcional.
- **horário semanal e pausas:** profissional, dia da semana, início, fim.
- **bloqueio:** profissional, início, fim, motivo.
- **cliente:** profissional, nome, WhatsApp, e-mail opcional, anotações.
- **agendamento:** profissional, cliente, início, fim, status (pendente, confirmado, cancelado, concluído, falta), valor total, código secreto da reserva, presença confirmada em.
- **item do agendamento:** serviço, opções, preço e duração gravados no momento (preserva o histórico).
- **administrador:** usuários com acesso ao painel da plataforma.

Regra do banco: restrição de exclusão por intervalo de tempo, por profissional, para agendamentos pendentes e confirmados.

## 8. Fluxos principais

### Agendar

1. A cliente abre o link, escolhe profissional (ou qualquer uma), serviço(s) e horário.
2. Informa nome e WhatsApp, aceita a política e passa pelo captcha.
3. Modo automático: agendamento confirmado. Modo com aprovação: pendente, segurando o horário.
4. Se dois agendamentos disputarem o mesmo horário, o banco recusa o segundo e a cliente escolhe outro.
5. A cliente recebe o link da reserva. A profissional é avisada por painel, push e e-mail.

### Aprovação (modo com aprovação)

A profissional aprova ou recusa no painel. A cliente vê o resultado no link da reserva.

### Cancelar e remarcar

Pelo link da reserva, até o prazo. Depois do prazo, só o botão de WhatsApp.

### Confirmação de presença

Um dia antes, o painel destaca as não confirmadas, com mensagem pronta de WhatsApp. A cliente confirma pelo link. A profissional decide se libera o horário.

### Manutenção

Ao informar o WhatsApp, o sistema sugere a faixa de preço conforme a última aplicação. A profissional confirma ou ajusta.

## 9. Segurança, privacidade e LGPD

- Regras de acesso por linha no banco: cada profissional só acessa os próprios dados. A dona só acessa o que a profissional liberar.
- A cliente nunca lê as tabelas diretamente. Usa funções controladas que devolvem só horários livres e a própria reserva.
- Código da reserva longo e aleatório.
- Captcha e limites por número e por IP.
- Consentimento no agendamento, política de privacidade e termos de uso. Exclusão de dados mediante pedido.
- Fotos públicas apenas do portfólio e dos serviços.
- Slugs reservados (admin, api, reserva, login e semelhantes) não podem ser usados.

## 10. Stack e infraestrutura

- **Front-end e PWA:** Next.js com TypeScript.
- **Banco, login e arquivos:** Supabase (Postgres, autenticação com Google e e-mail, armazenamento de fotos).
- **Hospedagem:** plano gratuito que permita uso comercial (candidato: Cloudflare). O plano gratuito da Vercel não permite uso comercial.
- **Captcha:** solução gratuita (candidato: Cloudflare Turnstile).
- **E-mail transacional:** provedor com plano gratuito (a definir). É o serviço que envia e-mails automáticos do sistema: recuperação de senha, convite de profissionais pela dona, avisos à profissional (novo agendamento, cancelamento, remarcação) e, se a cliente informar e-mail, confirmação com o link da reserva e lembrete. Exige configurar o domínio (SPF e DKIM) para os e-mails não caírem no spam.
- **Notificações:** Web Push (PWA).

Observação de custo: o plano gratuito do Supabase pausa projetos com pouca atividade e tem 500 MB de banco e 1 GB de arquivos. Ao ter as primeiras clientes pagantes, migrar para o plano Pro (cerca de 25 dólares por mês, a confirmar na página de preços).

## 11. Modelo de negócio

Cobrança das profissionais e estúdios feita **por fora** (Pix), com ativação manual pelo administrador. Período de teste configurável por conta. Cobrança automática fica para a fase 2.

## 12. Testes

- Cálculo de horários livres (horário semanal, bloqueios, intervalos, limites, vários serviços).
- Agendamentos simultâneos para o mesmo horário.
- Regras de acesso entre profissionais, dona e administrador.
- Fluxos de ponta a ponta: agendar, aprovar, cancelar, remarcar, confirmar presença.
- Teste em celulares reais (iPhone e Android), incluindo instalação do PWA e push.

## 13. Decisões tomadas

- [x] Agendamento automático e com aprovação, escolhido por profissional.
- [x] Estúdios e solo desde o início, solo como estúdio de uma pessoa.
- [x] Um domínio só, links por caminho.
- [x] Sem pagamento no sistema na v1, com estrutura para sinal por Pix depois.
- [x] Cliente sem conta nem verificação.
- [x] Sem API do WhatsApp.
- [x] PWA, sem app nativo na v1.
- [x] Supabase (Postgres) em vez de Firebase.
- [x] Cobrança manual das profissionais.
- [x] Login da profissional com Google e e-mail.
- [x] Fotos de antes e depois por cliente e controle de faltas ficam para a fase 2.
- [x] Nome: **TemHorarioAi**, com o logo escrito "Tem Horário Aí?". Domínio informado como disponível no Registro.br.

## 14. Decisões em aberto

- [ ] Registrar o domínio e conferir a marca no INPI.
- [ ] Provedor de e-mail transacional.
- [ ] Hospedagem final (Cloudflare ou outra).
- [ ] Duração do período de teste.
- [ ] Limites exatos de abuso (agendamentos simultâneos por número, tentativas por IP).
- [ ] Textos de política de privacidade e termos de uso.
- [ ] Lista de slugs reservados.
- [ ] Faixas padrão sugeridas para manutenção de cílios.
- [ ] Nível de sincronização com Google Agenda na fase 2.

## 15. Próximos passos

1. Revisar esta especificação e corrigir o que estiver diferente do que foi combinado.
2. Salvar no repositório como `docs/spec.md`.
3. Plano de implementação por etapas (base do banco e login, catálogo, agenda e disponibilidade, página pública e agendamento, link da reserva, painel e notificações, estúdios, administração).
