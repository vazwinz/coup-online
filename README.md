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

## Imagens das cartas

As ilustrações padrão são SVG próprios. Você pode subir suas próprias imagens pelo botão **Cartas** — elas ficam salvas **só no seu navegador** (localStorage), nunca neste repositório.

## Créditos

Implementação caseira, de fã. O jogo original **Coup** é de Rikki Tahta.
