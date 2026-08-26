# Planik · Análise de Visitação

BI de visitação dos stands Planik, com login por e-mail e visões por perfil.
Arquivo único (`index.html`) — sem build, sem servidor: hospeda em qualquer
lugar que sirva HTML estático (GitHub Pages).

## Como funciona o acesso

1. O admin libera um e-mail na engrenagem **⚙ Acessos** (dentro do BI), com o
   perfil e, quando for o caso, o vínculo (nome do gerente ou stand).
2. No primeiro acesso a pessoa digita o e-mail → aparece **Primeiro acesso** →
   ela define a senha (2 campos iguais, mínimo 6 caracteres).
3. Nos acessos seguintes: e-mail → senha → entra.
4. Só entra quem está liberado e **ativo**; o botão Desativar corta na hora.
5. Senhas ficam no **Firebase Auth** (nunca no código). Para redefinir uma
   senha: console do Firebase → Authentication → usuário → *Reset password*.

### Perfis e o que cada um vê

| Perfil | Visão |
|---|---|
| Administrador | tudo + engrenagem de acessos |
| Diretor House | só canal PLANIK VENDAS |
| Diretor Parcerias | só canal PLANIK PARCERIAS |
| Superintendente | só canal PLANIK VENDAS |
| Gerente | só as visitas do próprio nome (vínculo = gerente) |
| Coordenador Stand | só o próprio empreendimento (vínculo = stand) |

## Dados

Lidos **ao vivo** das planilhas "PLANIK Visitação Semanal 2026" (Google Sheets,
endpoint gviz CSV público) ao abrir a página; botão **↻ Atualizar** relê.
Se a leitura falhar, cai na fotografia embutida (26/08/2026) e o cabeçalho avisa.
Para incluir um empreendimento novo (ex.: TAJ Ibirapuera): acrescente
`{emp, id, gid}` na constante `SHEETS` do `index.html` e a cor em `EMP_META`.

## Configuração (uma vez só)

1. **Firebase**: crie um projeto em console.firebase.google.com →
   - Authentication → Sign-in method → ative **E-mail/senha**;
   - Firestore Database → criar (modo produção) → aba **Regras** → cole o
     conteúdo de `firestore-rules.txt` → Publicar;
   - Configurações do projeto → *Seus apps* → ícone **Web** → registre o app →
     copie o objeto `firebaseConfig` e cole em `FB_CONFIG` no `index.html`;
   - Authentication → Settings → **Domínios autorizados** → adicione o domínio
     do GitHub Pages (ex.: `SEUUSUARIO.github.io`).
2. **Primeiro admin**: no Firestore, crie manualmente a coleção `acessos` com um
   documento cujo ID é `jorge.lima@planik.com.br` e os campos:
   `email` (o mesmo), `perfil`: `admin`, `ativo`: `true`, `senhaDefinida`: `false`,
   `vinculo`: `null`. Depois disso, todo o resto se faz pela engrenagem no BI.
3. **GitHub Pages**: repositório → Settings → Pages → Branch `main` / raiz.

## Teste local (sem Firebase)

Abra o `index.html` direto do computador (duplo clique). Em `file://` o app
entra em **modo demo**: `admin@demo`, `house@demo`, `parcerias@demo`,
`super@demo`, `gerente@demo`, `stand@demo`, `novo@demo` (simula 1º acesso) —
qualquer senha. Nada de demo funciona na versão publicada (https).

## Honestidade sobre segurança

O login controla **quem entra na página e qual recorte vê**. Os dados em si
vêm de planilhas Google com link público (mesmo modelo do BI atual da Vercel) —
quem tiver o link direto da planilha continua vendo a planilha. Para blindar
os dados de verdade, o próximo passo seria movê-los para o Firestore com regras
por perfil (documentado no projeto como evolução).

## Estrutura

- `index.html` — app completo (login + BI + engrenagem + leitura do Sheets).
- `firestore-rules.txt` — regras de segurança para colar no console.
- Fontes de desenvolvimento e documentação completa do projeto: pasta
  "PROJETO BI VISITAÇÃO" (DOCUMENTACAO - PROJETO BI VISITACAO.md).
