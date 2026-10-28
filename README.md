# Coup

Jogo de blefe, dedução e golpe de estado, num único arquivo HTML. Dá pra jogar **contra bots** ou **online com os amigos**, direto no navegador.

**Jogar:** https://vazwinz.github.io/coup-online/

## Online

Quem cria a sala vira o **host** e a partida roda no navegador dele; os outros entram com um **código de 5 letras**. A conexão é direta entre os navegadores (WebRTC, via PeerJS), sem servidor próprio.

- Convite direto numa sala: `https://vazwinz.github.io/coup-online/?sala=CODIGO`
- Até 6 jogadores; o resto assiste. Bots completam a mesa.
- 15 s para contestar/bloquear, 45 s na sua vez. Caiu? Um bot assume até você voltar com o mesmo nome e código.

## Modos e variantes

- **Clássico** e **Mentalista** (no Golpe você chuta uma carta do alvo).
- Personagens: Assassino, Capitão, Condessa, **Burocrata** (no lugar do Duque) e **Bufão** (no lugar do Embaixador).

## Imagens das cartas

As ilustrações padrão são SVG próprios. Você pode subir suas próprias imagens pelo botão **Cartas** — elas ficam salvas **só no seu navegador** (localStorage), nunca neste repositório.

## Créditos

Implementação caseira, de fã. O jogo original **Coup** é de Rikki Tahta.
