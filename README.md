# Coup

Jogo de blefe, dedução e golpe de estado, num único arquivo HTML. Dá pra jogar **contra bots** ou **online com os amigos**, direto no navegador.

**Jogar:** https://vazwinz.github.io/coup-online/

## Online

Quem cria a sala vira o **host** e a partida roda no navegador dele; os outros entram com um **código de 5 letras**. A conexão é direta entre os navegadores (WebRTC, via PeerJS), sem servidor próprio.

- Na sala, **Convidar** manda o link pelo WhatsApp (ou copia). Quem abre o link cai direto na sala: `https://vazwinz.github.io/coup-online/?sala=CODIGO`
- Até 6 jogadores; o resto assiste. Bots completam a mesa.
- Na sua vez são **30 s no total** para ação, alvo e chute (voltar não zera o tempo); 15 s para contestar ou bloquear.
- Caiu? Um bot assume até você voltar **pelo mesmo navegador**. Na entrada o host te dá uma chave secreta, e só ela devolve a vaga: ninguém assume o seu lugar (nem vê as suas cartas) só digitando o seu nome. Recarregou a página ou o celular fechou o app? A tela inicial oferece voltar para a sala.
- Limitação conhecida: a partida roda no aparelho do host. Se ele fechar ou minimizar o jogo (principalmente no iPhone), a mesa trava para todo mundo.

## No celular

Instalável como app (PWA): abra o link no navegador e use **Adicionar à tela inicial** — abre em tela cheia, sem a barra do navegador. Durante a partida a tela não apaga, e se você sair do app e voltar, o jogo **reconecta sozinho** na mesma sala. O modo contra bots funciona offline depois da primeira abertura.

## Modos e variantes

- **Clássico** e **Mentalista** (no Golpe você chuta uma carta do alvo).
- Personagens: Assassino, Capitão, Condessa, **Burocrata** (no lugar do Duque) e **Bufão** (no lugar do Embaixador).

## Fim de partida

Quando a partida acaba, o jogo revela o que ficou escondido: quem blefou sem ninguém perceber, quem pegou mais blefes, quem contestou a verdade, e os números de cada um. Durante a partida esses dados ficam só com o host; ninguém recebe antes do fim. Também tem **placar da sala** entre as revanches (e da sessão, contra os bots).

## Hall da Fama

O Menu mostra só uma placa **SEMANA N**, sem dizer quem ganhou. Deslizando a placa pros lados aparecem as semanas anteriores (a mais recente vem primeiro). Tocando nela começa a **cerimônia** daquela semana, em clima de final: o palco sobe, o campeão aparece no escuro em cima de um pedestal, três batidas de coração, um instante de silêncio, e a luz acende: ele vira ouro com aura, **WINNER** abre atrás dele e o nome é gravado no pedestal. O som é todo grave e escuro (uma cama contínua que cresce com as batidas, 808 saturado, corte seco e o impacto), feito pra aparecer também no alto-falante do celular. Depois vem o **Hall da Fama**, com todos os campeões, fotos e contagem de títulos. Quem está com o nome do campeão atual aparece com uma **coroa** na sala e na mesa.

Link direto para mandar no grupo: `https://vazwinz.github.io/coup-online/?hall`. Ele abre na cerimônia e espera um toque para revelar o campeão (o navegador só libera som depois de um toque).

Para registrar o campeão da semana:

1. Coloque a foto na pasta `hall/` (em pé, de preferência já recortada).
2. Adicione uma entrada em `hall.json`, dentro de `campeoes`:

```json
{ "semana": 2, "nome": "Fulano", "foto": "hall/semana-02-fulano.webp" }
```

Foto **sem fundo** (PNG/WebP com transparência) vira estátua no pedestal: marque `"recorte": true`. Foto comum aparece como retrato emoldurado.

Outros campos opcionais: `"data"` (ex.: `"2026-11-09"`, aparece no Hall), `"frase"` (aparece embaixo da foto), `"apelidos"` (outros nomes que também ganham a coroa, ex.: `["Zac"]`) e `"enquadramento"` (ex.: `"50% 20%"`, se o rosto ficar cortado). Sem `"semana"`, as semanas são numeradas na ordem do arquivo. Entrada sem foto aparece com a inicial do nome.

Foto de celular costuma guardar a localização de onde foi tirada; tire esses dados antes de subir. O site atualiza em 1 a 2 minutos, e todo mundo vê o campeão novo na próxima vez que abrir o jogo.

## Placar da semana

A semana que está rolando aparece na ponta da placa do Menu, com uma bolinha vermelha de **ao vivo**. Tocando nela abre o **placar**: posição, nome e pontos, com empate dividindo a posição, o líder em destaque, coroa no campeão atual e quanto falta pra semana acabar. Quando o placar muda, a placa volta a brilhar até a pessoa olhar. O botão **Compartilhar** manda o placar pro grupo, com o link `https://vazwinz.github.io/coup-online/?placar`, que abre direto nele.

Os dados ficam em [`placar.txt`](placar.txt). Dá pra editar pelo próprio GitHub (abra o arquivo e toque no lápis) ou pelo note, com git:

```
semana: 2
fim: 13/11

Isaac 12
Vaz: 9
João Pedro - 7
```

- `semana`: o número da semana que está rolando. Sem ele, o placar não aparece.
- `fim` (opcional): o último dia da semana (`13/11`, `13/11/2026` ou `2026-11-13`). Com ele, o placar mostra quanto falta e, depois do dia, "Encerrada · campeão em breve".
- Um jogador por linha, nome e pontos. Vale `Isaac 12`, `Isaac: 12`, `Isaac - 12 pts` e lista numerada (`1. Isaac 12`). A ordem não importa: o jogo ordena. Linha com `#` é comentário. Nome repetido vale a última linha.

Quando a semana fechar, o campeão entra no `hall.json` e a placa daquela semana vira a cerimônia. Aí, no `placar.txt`, troque pra próxima semana e zere os pontos. Depois de salvar, o site atualiza em 1 a 2 minutos.

## Imagens das cartas

As ilustrações padrão são SVG próprios. Você pode subir suas próprias imagens pelo botão **Cartas** — elas ficam salvas **só no seu navegador** (localStorage), nunca neste repositório.

## Créditos

Implementação caseira, de fã. O jogo original **Coup** é de Rikki Tahta.
