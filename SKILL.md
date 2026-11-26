---
name: "criar-motion"
description: "Cria motion graphics profissional no Claude Chat com Remotion, em 3 etapas com aprovação em cada uma. Use sempre que o aluno marcar esta skill ou pedir um motion, vídeo animado, vinheta, texto animado, kinetic type, Reels animado ou videoaula em motion. Faz as perguntas, mostra o storyboard estático para aprovar a estética, anima com preview no Remotion e só exporta o MP4 quando o aluno pedir."
---

# Motion com Claude

Skill do curso Motion com Claude, criada por Naamã Rodrigues (NR7 Studio).

## As regras que valem sempre

1. **Perguntas primeiro.** Nenhum código antes de o aluno responder as perguntas da Etapa 0.
2. **Três etapas, sempre nesta ordem, cada uma com a aprovação explícita do aluno antes de seguir:**
   - Etapa 1: storyboard estático (imagens de cada cena) para aprovar a estética visual
   - Etapa 2: animação com preview no Remotion
   - Etapa 3: exportação em MP4
3. **Nunca pule etapa.** Mesmo que o aluno peça "o preview do vídeo", o primeiro passo é o storyboard estático. Nunca anime algo que a estética ainda não foi aprovada.
4. **Nunca gere o MP4 sem o aluno pedir.** Aprovar a animação não é pedir o MP4.

## Etapa 0. Perguntas (uma única rodada)

Pule o que o aluno já informou. Envie as perguntas numeradas e curtas e espere as respostas. Se faltar qualquer dado criativo, pergunte. Nunca assuma por conta própria.

1. Qual é a mensagem do motion e qual é o texto exato de cada cena?
2. Qual o formato? 1080x1920 (9:16), 1920x1080 (16:9), 1080x1080 (1:1) ou 1080x1350 (4:5)
3. Qual a duração?
4. Qual o estilo? Opções: explicativo, tipografia cinética, comercial, editorial, outro. Se o aluno não souber, descreva cada opção em uma linha e deixe ele escolher.
5. Quais as cores (em hex) e as fontes? Ou ele usa a identidade de uma marca?
6. Tem logo, imagens ou vídeos para usar? Peça para anexar.
7. Terá áudio? Sem áudio, ou com trilha ou locução que o aluno anexa.
8. Quer efeitos sonoros (SFX) no motion? Sim ou não. Se sim, eles entram junto com o preview da Etapa 2.
9. Tem alguma referência visual?

Regras das respostas:
- Números, nomes e dados que aparecem no motion vêm do aluno. Nunca invente.
- Padrão técnico quando o aluno não disser: 30 fps.

## Etapa 1. Storyboard estático (aprovação da estética)

Objetivo: o aluno aprovar como cada cena vai ficar, antes de existir qualquer movimento.

1. Crie o projeto Remotion com um `theme.ts` único (cores, fontes, easings, molas). Nenhuma cor ou fonte solta dentro de componente.
   - Estrutura: `src/index.ts`, `src/Root.tsx`, `src/scenes/`, `src/components/`, `src/theme.ts`. Arquivos do aluno em `public/` com `staticFile()`.
   - Fontes carregadas com `@remotion/google-fonts`. Nunca dependa de fonte do sistema.
2. Construa cada cena como um componente com o **layout final**, sem nenhuma animação.
3. Gere uma imagem por cena com `npx remotion still`, no formato escolhido, mostrando a cena completa.
4. Confira cada imagem antes de mostrar: fonte correta, texto inteiro dentro da tela, contraste, safe zone, nada cortado ou sobreposto. Corrija o que encontrar.
5. Envie as imagens ao aluno em ordem, cada uma identificada como "Cena 1", "Cena 2" e assim por diante, com uma linha curta dizendo o texto e as cores usadas.
6. Pergunte: "Você aprova a estética de cada cena ou quer mudar alguma?"

Ajustes da Etapa 1:
- Aplique só o que o aluno pediu e regenere só as cenas alteradas.
- Se o pedido for vago ("deixa mais bonito"), pergunte qual elemento e o que mudar.
- Repita até o aluno aprovar todas as cenas.
- Não escreva nenhuma animação nesta etapa.

## Etapa 2. Animação e preview no Remotion

Só começa depois da aprovação explícita da Etapa 1.

1. Dê movimento às cenas aprovadas. **Não altere layout, cores, fontes, posições finais ou textos aprovados.** Mudança de estética exige voltar à Etapa 1 para a cena afetada.
2. Aplique as regras de movimento abaixo.
3. Confira 4 quadros com `npx remotion still` (início, meio, fim e o trecho mais denso): texto fora do lugar, elemento visível antes de entrar ou depois de sair, saltos. Corrija antes de mostrar.
4. Se o aluno quis efeitos sonoros, adicione os SFX antes de entregar o preview (veja "Efeitos sonoros" abaixo).
5. Entregue o preview com o Remotion Player (`@remotion/player`), com play, pause e linha do tempo, tocando o áudio junto com a animação.
6. Escreva uma mensagem curta com uma frase do que ele está vendo. Se houver efeitos sonoros, diga quais 4 ou 5 momentos ele deve conferir com fone. Pergunte se aprova a animação e o som ou quer ajustes.

Ajustes da Etapa 2:
- Aplique só o que o aluno pediu e não mexa no resto.
- Se o pedido for vago, pergunte qual elemento e o que mudar.
- Depois de cada ajuste, repita a conferência de quadros e atualize o preview.
- Pedidos de som (trocar, tirar, abaixar ou mudar o momento de um efeito) também entram aqui.
- Repita até o aluno aprovar.

Não gere MP4 nesta etapa, nem para conferir.

### Regras de movimento

- **Zero easing linear.** Todo `interpolate` com easing e `extrapolate` clamp nos dois lados.
- **Entradas** com opacidade e deslocamento ou escala juntos. Fade sozinho é proibido.
- **Stagger:** palavras entram com 3 a 4 quadros de diferença, cards com 4 a 5, blocos com 6. Nada entra junto.
- **Saídas** existem e são mais rápidas que as entradas (cerca de 10 quadros contra 20).
- **Ritmo:** algo se move nos primeiros 15 quadros. Informação-chave e logo ficam pelo menos 1 segundo parados antes de sair. A primeira versão tende a ficar rápida demais, então comece mais lento.
- **Leitura:** tempo de tela do texto = 1,5 s + 1 s para cada 15 caracteres.
- **Quadro puro:** cada quadro é função pura do tempo. Sem `Math.random` sem seed, sem transição de CSS, sem timer. Todo tempo derivado do fps.
- **Proibido:** easing elástico sem motivo, explosão de partículas genérica, lens flare, tempo morto, cara de template.

### Efeitos sonoros (só se o aluno pediu)

- Só sons reais. Nunca sintetize som no código (oscilador, WebAudio, numpy): fica genérico e infantil.
- Fontes, nesta ordem:
  1. **Openverse** (https://openverse.org), só licença `cc0`. O Claude não acessa o Openverse sozinho. Use o Claude in Chrome no computador do aluno: abra `https://api.openverse.org/v1/audio/?q=<busca>`, baixe as prévias em MP3, junte tudo em um único zip com os créditos, pergunte "Posso baixar?" e peça que o aluno envie o zip para a conversa. A saída do `javascript_tool` é curta (cerca de 1 KB): guarde os dados em `window` e nunca passe áudio em base64 pela saída. Não funciona no celular. Se o aluno não tiver a extensão, avise e siga com as outras fontes.
  2. **Real SFX** (https://github.com/Clipwing/skills/tree/main/skills/real-sfx): rode `git clone https://github.com/Clipwing/skills` e siga o README para encaixar cada som no quadro exato e conferir sincronia, variedade e volume.
  3. **Earshot** (https://github.com/Leonxlnx/earshot): `pip install "earshot @ git+https://github.com/Leonxlnx/earshot"`. Gera sons de gravações em domínio público, offline. Use para o que o Openverse não tiver: toques, pops, sucção, moeda.
- Se alguma fonte não estiver acessível, diga qual e siga com as outras. Nunca invente o som.
- Salve os áudios em `public/sfx/` e toque com o componente de áudio do Remotion dentro de `<Sequence from={quadro}>`, para o preview e o MP4 usarem exatamente o mesmo som.
- Encaixe cada som no quadro da ação: pops nas entradas, whoosh nas transições (pico no corte), clique nos botões, impacto baixo nos destaques. Varie o arquivo, nunca repita o mesmo som em sequência.
- Volume: efeitos bem abaixo da locução (cerca de 9 dB) e ainda mais baixos enquanto a voz fala. Sem locução, mantenha os efeitos discretos.
- Você não ouve o áudio. Sempre liste os 4 ou 5 momentos que o aluno deve conferir com fone.
- Guarde os créditos das licenças e entregue junto com o motion.

### Estilo explicativo (videoaulas)
Se o aluno escolher o estilo explicativo, textos e elementos entram e ficam parados. Sem órbita, flutuação, tremido ou rotação. Sem glow e sem blur nos textos.

## Etapa 3. Exportação em MP4

- Só renderize quando o aluno pedir com clareza: "renderiza", "gera o MP4", "exporta o vídeo".
- Se ele apenas aprovar a animação, responda: "Animação aprovada. Quer que eu renderize o MP4?" e espere.
- Ao renderizar: `npx remotion render src/index.ts <Composicao> out/video.mp4 --codec h264 --crf 16 --overwrite`. Se o Remotion pedir um navegador, use o Chrome disponível no ambiente.
- O MP4 sai com o mesmo áudio aprovado no preview (locução, trilha e efeitos sonoros).
- Depois de renderizar, extraia 4 quadros do MP4 e confira antes de entregar.
- Entregue o arquivo com uma linha de contexto.

## Economia de tokens

- Respostas curtas. Não repita o código no chat.
- Faça as perguntas em uma única rodada.
- Não renderize nada que o aluno não pediu.