# Revisão de segurança de uma aplicação web (SaaS de agendamento)

> **Tipo:** revisão caixa-preta, passiva e não invasiva · **Data:** novembro de 2026
> **Habilidades:** análise de cabeçalhos HTTP, CSP, HSTS, TLS, OWASP, modelagem de ameaças, escrita de relatório técnico

## Contexto

Um colega da faculdade desenvolveu um SaaS de agendamento, em que pequenos negócios gerenciam agenda, clientes, caixa e estoque, e os clientes marcam horários por um link público. Ele me pediu uma avaliação de segurança da aplicação em produção, e eu entreguei um relatório com o que estava bom, o que podia melhorar e o que ainda precisava ser testado.

> **Confidencialidade:** para proteger a aplicação, o nome do sistema, o endereço, a tecnologia usada e os achados específicos foram omitidos. Este documento mostra apenas a metodologia e a estrutura do trabalho. O relatório completo foi entregue somente ao dono da aplicação.

## Escopo e regras

Antes de começar, combinei com o responsável o que eu poderia ou não fazer:

- **Permitido:** observar o que a aplicação já mostra publicamente para qualquer visitante.
- **Não permitido:** ataques ativos, força bruta, tentar burlar CAPTCHA ou mexer em dados reais.
- **Fora do escopo:** tudo que exige login ou acesso ao código-fonte. Esses itens ficaram listados como próximos passos, para serem testados só com contas de teste e autorização por escrito.

## O que avaliei

| Área | O que verifiquei |
|---|---|
| Cabeçalhos de segurança | CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, políticas cross-origin |
| Transporte | Uso obrigatório de HTTPS e configuração de TLS |
| Infraestrutura | Presença de CDN e WAF, e se o servidor de origem ficava exposto |
| Exposição de informação | Arquivos públicos comuns e cabeçalhos que revelam a tecnologia usada |
| Autenticação | Proteções contra automação nas páginas de login e cadastro |
| Cache | Se páginas com conteúdo de usuário podiam ficar guardadas em cache |

## Como o relatório foi organizado

1. **Resumo executivo:** a visão geral em poucas linhas, para quem não é da área.
2. **Escopo e método:** o que foi e o que não foi avaliado, e por quê.
3. **Pontos positivos:** os controles que já estavam corretos. Isso ajuda o responsável a saber o que manter.
4. **Achados e recomendações:** cada item com severidade (média, baixa ou informativa), o risco explicado em linguagem simples e a correção sugerida.
5. **Riscos a validar com credenciais:** os riscos de maior impacto para esse tipo de sistema, que não dava para testar de fora.
6. **Checklist de correções:** tudo em ordem de prioridade, para virar uma lista de tarefas.
7. **Próximos passos e ética:** o que seria preciso para uma análise mais profunda.

## Resultado

- A aplicação tinha uma **boa postura de segurança** na parte pública, e **não encontrei nenhuma vulnerabilidade crítica** no escopo combinado.
- As recomendações foram de **hardening**, ou seja, ajustes para deixar uma base que já era boa ainda mais resistente.
- O relatório deixou claro que o **maior risco para um sistema com várias empresas na mesma plataforma** é uma empresa conseguir ver os dados de outra. Esse tipo de falha (IDOR/BOLA) só pode ser testado com login, e virou a prioridade número 1 para uma próxima etapa.

## Riscos que mapeei para esse tipo de sistema

Sem entrar em detalhes da aplicação, estes são os pontos que considerei mais importantes para qualquer SaaS de agendamento com várias empresas:

- **Isolamento entre empresas:** uma conta nunca pode acessar a agenda, os clientes ou o caixa de outra, mesmo trocando IDs na URL ou na API (OWASP API1: BOLA).
- **Link público de agendamento:** limite de requisições contra spam e cuidado com campos de texto livre, que podem levar a XSS armazenado no painel do dono.
- **Autenticação:** bloqueio após várias tentativas, flags seguras nos cookies de sessão e opção de 2FA.
- **Dados pessoais e financeiros:** criptografia, logs sem dados pessoais e exportações protegidas, pensando também na LGPD.
- **Mass assignment:** os formulários não podem aceitar campos como "admin" ou "plano" enviados pelo usuário.

## O que aprendi

- Um bom relatório não é só uma lista de problemas. Mostrar o que já está certo ajuda o responsável a confiar no trabalho e a priorizar.
- Deixar claro o que **não** foi testado é tão importante quanto o que foi. Sem isso, o dono poderia achar que o sistema está totalmente seguro.
- Os riscos mais graves de uma aplicação nem sempre aparecem de fora. Em um sistema com várias empresas, a separação dos dados entre elas é o ponto principal.
- Segurança começa pela autorização: só testei o que o responsável permitiu, e deixei combinado por escrito o que precisaria de permissão extra.
