# Cerimônia da lâmpada (Gustavo, Semana 2): plano de implementação

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** segundo estilo de cerimônia ("lampada") pro Gustavo, campeão da Semana 2: escuro, lâmpada falhando, cartas do Bufão caindo nas piscadas vazias e ele aparecendo sem alvoroço, com um único som agudo ecoando.

**Architecture:** cada campeão do hall.json escolhe o estilo (`"cerimonia": "lampada"`; sem o campo, fica "ouro", o do Isaac). A mesma tela `#cer` ganha a classe `t-lampada`, com os elementos próprios do estilo (fio, lâmpada, cone de luz, cartas) e uma linha do tempo própria, que reaproveita os passos `s5`, `s7`, `s8` e `fin` da cerimônia atual. Os sons novos (`tick` e `ping`) entram no `SFX`.

**Tech Stack:** `index.html` (HTML, CSS e JS num arquivo só), Web Audio, Python com PIL, numpy e scipy pra imagem, e testes Playwright em `/tmp/claude-0/-home-claude/0836fa7e-8cbd-55da-a679-3f97817f2e04/scratchpad/harness` (servidores locais: 8080 jogo, 9000 PeerJS, 8081 v1).

**Spec:** `docs/superpowers/specs/2026-11-16-cerimonia-gustavo-design.md`

## Global Constraints

- A cerimônia do Isaac (estilo "ouro") não muda: `test-hall.cjs` (77 checks) continua passando.
- O estilo vem do campo `cerimonia` do hall.json. Só `"lampada"` é válido; sem o campo, ou com valor desconhecido, fica `"ouro"`.
- No estilo lâmpada não entram WINNER, dourado, faíscas, chuva, estrondo, batidas nem a cama de som (`SFX.hold('aura')`).
- Com animação reduzida (`reduced()`), vai direto pro final. Pelo link `?hall`, espera o toque antes das piscadas.
- Nada de `mix-blend-mode`, `clip-path` ou transform em `.cer-col` (regra do pedestal que piscava, guardada no test-hall).
- Imagem: a cabeça do meio, embaixo, de `/mnt/user-data/attachments/0836fa7e-8cbd-55da-a679-3f97817f2e04/9c4cc5ec-image.png` (1312×1199, fundo preto). Fica mais escura e menos nítida que o original e é salva sem metadados.
- Textos: o nome em caixa alta no pedestal ("GUSTAVO") e o subtítulo "Campeão · Semana 2".
- `VERSION` sobe pra uma versão nova antes de publicar. Só publica depois do ok do Vaz no vídeo.

## Review Focus

1. **Pular (toque ou Esc) no meio das piscadas:** vai pro final com a luz acesa, sem cartas, um único `ping` e nenhuma piscada agendada rodando depois. Teste na Task 3, seção "pular".
2. **Fechar e abrir outra cerimônia logo em seguida** (a do Isaac): nada do estilo lâmpada pode vazar (classe, cartas, luz, piscadas ociosas). Teste na Task 3, seção "fechar e reabrir".
3. **Imagem que não carrega:** cai no retrato com a inicial, e a sequência termina normalmente. Teste na Task 3, seção "imagem quebrada".
4. **Som desligado:** `tick` e `ping` respeitam o mudo, como os outros sons. Teste na Task 2.
5. **Celular estreito (360×740) e tela baixa (375×667):** lâmpada, busto, nome, subtítulo e botões cabem na tela, sem rolagem lateral. Teste na Task 3, seção "telas pequenas".

---

### Task 1: Dados e imagem do Gustavo

**Files:**
- Modify: `index.html`, `normHall` (cerca das linhas 2379-2402)
- Create: `hall/semana-02-gustavo.webp`, `hall/semana-02-gustavo-rosto.webp`, `.../scratchpad/tools/gustavo.py`
- Modify: `hall.json`, `placar.txt`
- Test: `.../scratchpad/harness/test-lampada.cjs` (novo), seção "dados"

**Interfaces:**
- Produces: cada item de `HALL.list` tem `tema: 'ouro' | 'lampada'`. A imagem é uma WebP RGBA com fundo transparente, com uns 720 px de largura e a base do pescoço na borda de baixo.

- [ ] **Step 1: escrever o teste que falha**

```js
// dados: estilo por campeão
const t = await ev(p, () => normHall({ campeoes: [{ nome: 'A', semana: 1 }, { nome: 'B', semana: 2, cerimonia: 'lampada' }, { nome: 'C', semana: 3, cerimonia: 'xyz' }] }).map(e => [e.nome, e.tema]));
ok(JSON.stringify(t) === '[["C","ouro"],["B","lampada"],["A","ouro"]]', 'estilo da cerimônia por campeão');
// imagem (python chamado pelo teste): existe, tem alfa, cantos transparentes, < 220 KB, sem EXIF/XMP,
// e é mais escura que o recorte original: luminância média dos pixels opacos <= 0.8 x a do original
```

- [ ] **Step 2: rodar e ver falhar.** Comando: `node test-lampada.cjs`. Esperado: FAIL em "estilo da cerimônia por campeão" (tema undefined) e na imagem (arquivo não existe).
- [ ] **Step 3: implementar**
  - `normHall`: `tema: x.cerimonia === 'lampada' ? 'lampada' : 'ouro'`.
  - `gustavo.py`: recorta a cabeça do meio, embaixo (caixa de partida x 430-890, y 585-1199, ajustar olhando). O alfa é o maior entre a luminância suavizada (cabelo) e a silhueta preenchida (rosto e pescoço opacos). Escurece (×0,72, gama 1,15, realces comprimidos), reduz pra uns 720 px e borra de leve (raio 0,6). Gera também o rosto em 256×256. Os valores são ponto de partida, ajustados na Task 5.
  - `hall.json`: `{"semana": 2, "nome": "Gustavo", "foto": "hall/semana-02-gustavo.webp", "rosto": "hall/semana-02-gustavo-rosto.webp", "recorte": true, "cerimonia": "lampada"}`.
  - `placar.txt`: `semana: 3`, sem jogadores.
- [ ] **Step 4: rodar e ver passar.** Esperado: PASS nas duas checagens.
- [ ] **Step 5: commit** `Gustavo no Hall (semana 2) e estilo de cerimônia por campeão`

### Task 2: Sons `tick` e `ping`

**Files:**
- Modify: `index.html`, objeto `L` do `SFX` (depois de `slam`)
- Test: `.../scratchpad/harness/audio-check.cjs`, seção nova

**Interfaces:**
- Produces: `SFX.play('tick')`, um estalo elétrico bem baixinho, e `SFX.play('ping')`, um agudo com eco.

- [ ] **Step 1: escrever o teste que falha.** Renderizar offline com `SFX._L.ping` e `SFX._L.tick`:
  - `ping`: pico abaixo de -1 dBFS. Ecos visíveis: a janela de 100 ms em +0,38 s e em +0,76 s do início é mais forte que a de +0,57 s. Abaixo de -60 dB depois de 4 s.
  - `tick`: pico abaixo de -24 dBFS e som acabando em menos de 0,15 s.
  - No mudo: `SFX.play('ping')` não cria som (o `play` já sai cedo com `muted`; a checagem é que `ping` e `tick` passam pelo `play`, como os outros).
- [ ] **Step 2: rodar e ver falhar.** `node audio-check.cjs`. Esperado: FAIL (`SFX._L.ping` não existe).
- [ ] **Step 3: implementar** `tick(B,t)` e `ping(B,t)` no `L`.
  - `ping`: seno em Mi6 (1318,5 Hz), com a oitava de baixo mais fraca. Ataque de 5 ms e decaimento de 1,4 s. O eco vem de um `DelayNode` de 0,38 s com realimentação 0,42 e passa-baixa de 2400 Hz no laço; o ganho da realimentação cai a 0 até t+3,2 s, pra o laço morrer sozinho.
  - `tick`: ruído em passa-faixa de 3 kHz por uns 10 ms (g 0,05) mais um zumbido em serra de 100 Hz com passa-baixa de 400 Hz por 70 ms (g 0,015).
- [ ] **Step 4: rodar e ver passar.**
- [ ] **Step 5: commit** `Sons da lâmpada: estalo e agudo com eco`

### Task 3: A cerimônia da lâmpada (tela e sequência)

**Files:**
- Modify: `index.html`:
  - HTML do `#cer`: `.cer-lamp` (fio e lâmpada), `.cer-cone` e `.cer-cards`, antes de `.cer-col`.
  - CSS: bloco novo `.cer.t-lampada …`.
  - JS: `LAMP_T`, `lampLit`, `lampCards`, `lampFlicker`, `lampReveal`, `lampIdle` e ajustes em `openCeremony`, `cerFinish`, `closeCeremony` e no clique do `#cer`.
- Test: `test-lampada.cjs`, seções "sequência", "pular", "link ?hall", "reduzido", "fechar e reabrir", "imagem quebrada", "telas pequenas" e "piscada depois"

**Interfaces:**
- Consumes: `e.tema` (Task 1), `SFX.play('tick' | 'ping')` (Task 2).
- Produces:
  - Classes no `#cer`: `t-lampada` (estilo), `lit` (luz acesa), `s5` (ele está lá), `s7` (nome), `s8` (botões), `fin` (final direto).
  - `CER.go`: função que começa a sequência depois do toque no link `?hall`. No ouro é `()=>{cerBedStart();cerBeats();}`; na lâmpada, `lampFlicker`.
  - `LAMP_T = {dark: 900, flick: [[70,120],[50,600],[180,90],[40,900],[380,140],[60,1100]], name: 1600, act: 2600, idle: [4000, 9000]}`: pares [acesa, apagada] em ms. Cada par toca um `tick` quando acende. A revelação vem depois do último par apagado (por volta de 4,6 s).
  - `lampLit(on: boolean)`: liga ou desliga a classe `lit`.
  - `lampCards()`: põe de 6 a 8 cartas `cardHTML('bufao','md')` em `.cer-cards`, caindo sem parar. Só aparecem com `lit`.

- [ ] **Step 1: escrever os testes que falham** (cerimônia de um fixture com o Gustavo em "lampada" e o Isaac em "ouro"):
  - **sequência:**
    - `s4` nunca aparece, e `lit` alterna pelo menos 3 vezes antes de `s5`.
    - `s5` acontece entre 3,6 e 6 s depois de abrir.
    - Antes de `s5` existem pelo menos 5 `.cer-cards .card[title="Bufão"]`, e depois não existe nenhuma.
    - WINNER, raios, refletores e aura ficam invisíveis (opacidade 0 ou `display:none`). Nenhum `.cer-spark` e nenhum `#fx .fxflash`.
    - Sons: pelo menos 3 `tick` antes de exatamente 1 `ping`. Nenhum `swell`, `hum`, `rumble`, `heart`, `braam` ou `slam`, e nenhum `SFX.hold`.
    - A imagem tem opacidade 0 antes de `s5` e acima de 0,5 depois.
    - O nome aparece em caixa alta ("GUSTAVO") e o subtítulo é "Campeão · Semana 2".
    - `s7` vem uns 1,6 s depois de `s5` (±0,4 s), depois `s8`, e `lit` continua ligado em `s8`.
  - **pular:** um toque durante as piscadas leva pro final (`s8` e `lit`, sem cartas) com um único `ping`. Nada de `lit` mudar nos 2 s seguintes, além das piscadas ociosas que só começam a partir de `LAMP_T.idle[0]`.
  - **link ?hall:** o texto "Toque para revelar" aparece e `lit` não muda antes do toque. Depois do toque, as piscadas começam.
  - **reduzido:** o final aparece de cara (`s8`, `lit`, imagem visível, sem cartas).
  - **fechar e reabrir:** fechando no meio e abrindo o Isaac, o `#cer` fica sem `t-lampada`, sem `lit` e sem cartas, e `lit` não muda mais. A sequência do Isaac anda normal (`s4` aparece).
  - **imagem quebrada:** com a foto em 404, fica a classe `frame` com a inicial, e a sequência chega em `s8`.
  - **telas pequenas** (360×740 e 375×667): lâmpada, nome, subtítulo e botões ficam dentro da tela, sem rolagem lateral.
  - **piscada depois:** até 10 s depois de `s8`, a luz apaga pelo menos uma vez e volta.
- [ ] **Step 2: rodar e ver falhar.** `node test-lampada.cjs`. Esperado: FAIL em "sequência" (abre o estilo ouro).
- [ ] **Step 3: implementar**
  - `openCeremony` faz o comum (figura, textos, foco). Com `e.tema === 'lampada'`, põe `cer t-lampada` (mais `frame` se não for recorte), define `CER.go = lampFlicker` e chama `lampFlicker` (ou espera o toque, se `CER.wait`). No reduzido, adiciona `up s5 s7 s8 fin lit` e para.
  - `lampFlicker` agenda os pares com `cerAt`; as cartas só existem até a revelação.
  - `lampReveal` adiciona `s5`, toca `ping`, tira as cartas, agenda `s7` e `s8` e começa `lampIdle`.
  - `lampIdle` agenda piscadas curtas (60 a 150 ms apagada, `tick`) a cada 4 a 9 s, também com `cerAt`, pra `closeCeremony` limpar tudo.
  - `cerFinish` do estilo lâmpada: limpa os timers, tira as cartas, adiciona `fin up s5 s7 s8 lit`, toca `ping` se ainda não tinha revelado e começa `lampIdle`.
  - O clique do `#cer` com `CER.wait` chama `CER.go()`.
  - CSS:
    - A tela aparece por opacidade, não sobe.
    - Esconde raios, refletores, aura, névoa, poeira, WINNER, liga e semana do topo.
    - Lâmpada pendurada no centro do topo. O cone é um gradiente radial suave, visível só com `lit`.
    - Cartas visíveis só com `lit`, caindo com rotação.
    - Pedestal preto com linha e nome em prata. O nome aparece num fade lento, sem `nameIn`.
    - As cópias `.g1` e `.g2` ficam escondidas.
- [ ] **Step 4: rodar e ver passar.** Rodar também `test-hall.cjs` (o Isaac sem mudança).
- [ ] **Step 5: commit** `Cerimônia da lâmpada`

### Task 4: Hall sem dourado no estilo lâmpada, README

**Files:**
- Modify: `index.html` (`renderHall`, linhas do `.pc`, e CSS `.trophy .pc.cut.lamp` / `.hof .pc.cut.lamp`), `README.md` (seção do Hall)
- Test: `test-lampada.cjs`, seção "hall"

**Interfaces:**
- Consumes: `e.tema`.

- [ ] **Step 1: teste que falha.** No Hall, o cartão do Gustavo tem `.pc.cut.lamp`, sem o gradiente dourado (o `background-image` computado não tem `rgb(212, 160, 70`). O do Isaac continua com `.pc.cut`, sem `.lamp`.
- [ ] **Step 2: rodar e ver falhar.**
- [ ] **Step 3: implementar.** A classe `lamp` entra no `.pc` (destaque e miniaturas) quando `tema === 'lampada'`. Fundo preto com brilho neutro e sombra cinza. O README ganha o campo `cerimonia` (`"lampada"` ou nada).
- [ ] **Step 4: rodar e ver passar.**
- [ ] **Step 5: commit** `Hall: cartão sem dourado no estilo lâmpada`

### Task 5: Vídeo pro Vaz, ajuste fino e publicação

**Files:**
- Modify: `index.html` (`VERSION`, e `LAMP_T` se precisar), `.../scratchpad/tools/gustavo.py` (valores), `.../scratchpad/harness/shift-check.cjs` e scripts de vídeo (tocar a placa certa)

- [ ] **Step 1: gravar a cerimônia com som** (video5.cjs + render-log5.cjs, tocando `#wkTrack .wk.newest`, que agora é o Gustavo) e olhar os quadros. A estátua precisa ficar escura e sem brilho demais na luz fraca, e as piscadas precisam ler como lâmpada falhando. Ajustar e regravar se precisar.
- [ ] **Step 2: rodar tudo.** `test-hall`, `test-placar`, `test-anim`, `test-lampada`, `test-online`, `test-solo`, `audio-check` e `shift-check`, este último ajustado pra tocar o Isaac (`#wkTrack .wk:first-child`). Esperado: TUDO OK em todos.
- [ ] **Step 3: mandar o vídeo pro Vaz e esperar o ok.**
- [ ] **Step 4: com o ok,** subir `VERSION`, fazer o commit e o push de `hall-da-fama`, fast-forward da `main` (depois do `git fetch`, caso ele tenha mexido no placar), push e acompanhar o deploy do Pages até `success`.
