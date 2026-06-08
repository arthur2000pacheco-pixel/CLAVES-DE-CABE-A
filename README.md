// ---JOGO---
let estado= "MENU";
let bossAtual=0;
let tempoBoss = 0;
let tremorTela = 0;
let ritmoEscolhido = "4/4"; 
let multiplicadorRitmo = 1.0;
let tempoBarra = 0;
let imgjogador, imgBoss, imgChao, imgFundo;
let jogador; 
let chefao; 
let tirosjogador = [];
let tirosBoss = []; 
let particulas = [];
let textosDano = []; 
let minions = []; 
let barrasCompasso = [];
let repetirFase = false;
let repeticaoAtiva = false;
let mostrarJumpScare = 0;
let proximoIndice = 0;
let botaoPause = { x: 20, y: 110, w: 120, h: 40 };
let musicas = [];
let musicaAtual = 0;
let somAtivo = true;

let botoesPause = [
  { texto: "🔊 SOM: ON", acao: "som" },
  { texto: "🔁 REINICIAR", acao: "restart" },
  { texto: "🚪 SAIR", acao: "sair" }
];

const VEL_NOTAS = { 
"𝅜": 0.5, // Semibreve (1 nota a cada 8 tempos) 
"𝅝": 1, // Semibreve (1 nota a cada 4 tempos) 
"𝅗𝅥": 2, // Mínima (2 notas a cada 4 tempos) 
"♩": 4, // Semínima (4 notas a cada 4 tempos - 1 por tempo)      
  "𝅘𝅥𝅮": 8, // Colcheia (8 notas a cada 4 tempos - 2 por tempo) 
"𝅘𝅥𝅯": 16, // Semicolcheia (16 notas a cada 4 tempos - 4 por tempo) 
  "𝅘𝅥𝅰": 32, // Fusa (32 notas a cada 4 tempos - 8 por tempo) 
"𝅘𝅥𝅲": 64, // Semifusa (64 notas a cada 4 tempos - 16 por tempo) 
"quartefusa": 128 // Quartefusa( 128 notas a cada 4 tempos - 32 por tempo)
};
  // PAUSAS
// 🎵 PAUSAS MUSICAIS
function ehPausa(n) {
  return ["𝄺","𝄻","𝄼","𝄽","𝄾","𝄿","𝅀","𝅁","𝅂"].includes(n);
}
const DURACAO_NOTAS = { "𝅝": 4, "𝅗𝅥": 2, "♩": 1, "𝅘𝅥𝅮": 0.5, "𝅘𝅥𝅯": 0.25, "𝅘𝅥𝅰": 0.125, "𝅘𝅥𝅲": 0.0625 };
const dadosBosses = [
  { nome: "CLAVE DE SOL (A Diva Macabra)", hp: 100, tipo: "sol", cor: "#ff2e63", icone: "𝄞" }, 
  { nome: "CLAVE DE FÁ (O Titã Subterrâneo)", hp: 120, tipo: "fa", cor: "#2b2d42", icone: "𝄢" }, 
  { nome: "CLAVE DE DÓ (A Entidade Média)", hp: 140, tipo: "do", cor: "#f4a261", icone: "𝄡" },
  { nome :"CLAVE DE PERCUSSÃO(TOQUE SEM ERRO)",hp: 160, tipo: "percussão", cor:"#bdbdbd",icone: "❚❚"},
  { nome: "BREVE & SEMIBREVE (A Dinastia do Tempo)", hp: 180, tipo: "Selo Sonoro", cor: "#ffeaa7" , icone: "𝅜𝅝" }, 
  { nome: "MÍNIMA & SEMÍNIMA (Os Irmãos Cadência)", hp: 200, tipo: "minima_seminima", cor: "#8ac926", icone: "𝅗𝅥♩" }, 
  { nome: "COLCHEIA & SEMICOLCHEIA (A Velocidade)", hp: 220, tipo: "colcheia_semicolcheia", cor: "#00f5d4", icone: "𝅘𝅥𝅮𝅘𝅥𝅯" }, 
  { nome: "FUSA & SEMIFUSA (O Terror dos músicos)", hp: 240, tipo: "fusa e semifusa", cor: "#ff006e", icone: "𝅘𝅥𝅰𝅘𝅥𝅲" }, 
  { nome: "QUARTEFUSA", hp: 260, tipo: "quartefusa", cor: "#7b00ff", icone: "??" },
  // 🎵 BOSSES DE PAUSA
{ nome: "PAUSA DE BREVE (O Silêncio Absoluto)", hp: 180, tipo: "pausa_do tempo", cor: "#e0e0ff", icone: "𝄺" },
{ nome: "PAUSA DE SEMIBREVE (O Vazio)", hp: 170, tipo: "pausa_ do tempo", cor: "#b8c0ff", icone: "𝄻" },
{ nome: "PAUSA DE MÍNIMA (O Suspense)", hp: 160, tipo: "pausa_cadencia", cor: "#8e9aff", icone: "𝄼" },
{ nome: "PAUSA DE SEMÍNIMA (O Corte)", hp: 150, tipo: "pausa_cadencia", cor: "#5f6fff", icone: "𝄽" },
{ nome: "PAUSA DE COLCHEIA (O Reflexo)", hp: 140, tipo: "pausa_ velocidade", cor: "#3a4fff", icone: "𝄾" },
{ nome: "PAUSA DE SEMICOLCHEIA (O Pulso)", hp: 130, tipo: "pausa_velocidade", cor: "#1f2fff", icone: "𝄿" },
{ nome: "PAUSA DE FUSA (O Tremor)", hp: 120, tipo: "pausa_terror", cor: "#0a1aff", icone: "𝅀" },
{ nome: "PAUSA DE SEMIFUSA (O CAOS FINAL)", hp: 200, tipo: "pausa_terror", cor: "#00d4ff", icone: "𝅁" }
];
function desenharMenuPause() {
  fill(0, 0, 0, 200);
  rect(0, 0, width, height);

  textAlign(CENTER, CENTER);
  textSize(40);
  fill(255);
  text("⏸ PAUSADO", width / 2, height / 2 - 120);

  for (let i = 0; i < botoesPause.length; i++) {
    let b = botoesPause[i];

    let x = width/2 - 120;
    let y = height/2 - 40 + i * 70;
    let w = 240;
    let h = 50;

    let over =
      mouseX >= x && mouseX <= x + w &&
      mouseY >= y && mouseY <= y + h;

    stroke(over ? color(0,255,204) : 80);
    fill(over ? color(20,25,40) : 15);
    rect(x, y, w, h, 8);

    noStroke();
    fill(255);
    textSize(18);
    text(b.texto, x + w/2, y + h/2);

    // atualizar texto do som
    if (b.acao === "som") {
      b.texto = somAtivo ? "🔊 SOM: ON" : "🔇 SOM: OFF";
    }

    b.x = x;
    b.y = y;
    b.w = w;
    b.h = h;
  }
}
function preload() {
  musicas[0] = loadSound("musica1.mp3");
  musicas[1] = loadSound("musica2.mp3");
  musicas[2] = loadSound("musica3.mp3");
  musicas[3] = loadSound("musica4.mp3");
}
function tocarMusica(index) {

  // parar todas
  for (let m of musicas) {
    if (m && m.isPlaying()) {
      m.stop();
    }
  }

  musicaAtual = index;

  if (somAtivo && musicas[index]) {
    musicas[index].loop();
  }
}
function setup() { 
  createCanvas(900, 600); 
  textFont('Courier New');
  tocarMusica(0);
  jogador = new Player();
  proximoBoss(0); 
}

function draw() { 
  if (estado === "MENU") { 
    telaInicioMetronome();
    return; 
  }
  
  if (imgFundo) { 
    image(imgFundo, 0, 0, width, height);
  } else {
    background(15, 15, 25); // Fundo escuro aprimorado para contraste neon 
    stroke(30, 30, 45); 
    strokeWeight(1); 
    for (let i = -height; i < width; i += 20) { line(i, 0, i+ height, height); } 
  }
  
  push(); 
  if (tremorTela > 0) { 
    translate(random(-tremorTela, tremorTela), random(-tremorTela, tremorTela)); 
    tremorTela *= 0.85; if (tremorTela < 0.5) tremorTela = 0; 
  }
  desenharCenario();
 if (estado === "JOGANDO") { 
  atualizarJogo(); 
  renderizarJogo(); 
} else if (estado === "PAUSADO") {
  renderizarJogo();
  desenharMenuPause();
  fill(0,0,0,180);
  rect(0,0,width,height);

  fill(255);
  textAlign(CENTER, CENTER);
  textSize(40);
  text("⏸ PAUSADO", width/2, height/2);
} else { 
    telaFimDeJogo(); 
  } 
  pop();
  desenharInterface();
  desenharBotaoPause();
}
function desenharBotaoPause() {
  let over =
    mouseX >= botaoPause.x &&
    mouseX <= botaoPause.x + botaoPause.w &&
    mouseY >= botaoPause.y &&
    mouseY <= botaoPause.y + botaoPause.h;

  stroke(over ? color(0,255,204) : 80);
  fill(over ? color(20,25,40) : 10);
  rect(botaoPause.x, botaoPause.y, botaoPause.w, botaoPause.h, 6);

  noStroke();
  fill(255);
  textAlign(CENTER, CENTER);
  textSize(14);

  if (estado === "PAUSADO") {
    text("▶ CONTINUAR", botaoPause.x + botaoPause.w/2, botaoPause.y + botaoPause.h/2);
  } else {
    text("⏸ PAUSAR", botaoPause.x + botaoPause.w/2, botaoPause.y + botaoPause.h/2);
  }
}
function mousePressed() {

  // BOTÃO PAUSE NORMAL
  let clicouBotao =
    mouseX >= botaoPause.x &&
    mouseX <= botaoPause.x + botaoPause.w &&
    mouseY >= botaoPause.y &&
    mouseY <= botaoPause.y + botaoPause.h;

  if (clicouBotao) {
    if (estado === "JOGANDO") estado = "PAUSADO";
    else if (estado === "PAUSADO") estado = "JOGANDO";
  }

  // MENU DE PAUSA
  if (estado === "PAUSADO") {
    for (let b of botoesPause) {
      let clicou =
        mouseX >= b.x &&
        mouseX <= b.x + b.w &&
        mouseY >= b.y &&
        mouseY <= b.y + b.h;

      if (clicou) {

        // 🔊 SOM
       if (b.acao === "som") {
  somAtivo = !somAtivo;

  if (!somAtivo) {
    // 🔇 para todas as músicas
    for (let m of musicas) {
      if (m && m.isPlaying()) {
        m.stop();
      }
    }
  } else {
    // 🔊 volta a música atual
    tocarMusica(musicaAtual);
  }
}

        // 🔁 REINICIAR
        if (b.acao === "restart") {
          estado = "JOGANDO";
          bossAtual = 0;
          proximoBoss(0);

          tirosBoss = [];
          tirosjogador = [];
          minions = [];
          barrasCompasso = [];
          textosDano = [];
          particulas = [];

          jogador.resetarFase();
        }

        // 🚪 SAIR
        if (b.acao === "sair") {
          estado = "MENU";
        }
      }
    }
  }
}
function telaInicioMetronome() {
  background(10, 10, 20);
 stroke(25, 25, 40); 
 strokeWeight(2); 
 for (let i = 0; i < width; i += 40) { line(i, 0, i, height); 
  }
  fill(255); noStroke(); textAlign(CENTER, CENTER); 
  textSize(32); textStyle(BOLD); text("METRÔNOMO BATIDAS", width / 2, height / 2 - 140);
  textSize(16); textStyle(NORMAL); fill(150); text("Selecione o ritmo do concerto:", width / 2, height / 2 - 90);
  
  let botoes = [
    { nome: "COMPASSO 4/4 (Estrela Dourada)", ritmo: "4/4", mult: 1.0, x: width/2 - 140, y: height/2 - 100 }, 
    { nome: "COMPASSO 6/8 (Pulso Elétrico)", ritmo: "6/8", mult: 1.3, x: width/2 - 140, y: height/2 -50 }, 
    { nome: "COMPASSO 3/4 (Plasma Esmeralda)", ritmo: "3/4", mult: 0.8, x: width/2 - 140, y: height/2 + 0 }, 
    { nome: "COMPASSO 2/4 (Fogo Magenta)", ritmo: "2/4", mult: 1.2, x: width/2 - 140, y: height/2 + 50 },
    { nome: "COMPASSO c (agua gelada)", ritmo: "c", mult: 1.3, x: width/2 - 140, y: height/2 + 100 },
    { nome: "COMPASSO 9/8 (Chama de Plasma)", ritmo: "9/8", mult: 1.3, x: width/2 - 140, y: height/2 + 150 },
    { nome: "COMPASSO 12/8 (Lava)", ritmo: "12/8", mult: 1.3, x: width/2 - 140, y: height/2 + 200 },
  ];
  if (mostrarJumpScare > 0) {
  push();

  fill(0, 0, 0, 200);
  rect(0, 0, width, height);

  textAlign(CENTER, CENTER);
  textSize(120);
  fill(255);
  text(":||", width/2, height/2);

  pop();

  mostrarJumpScare--;
}
  botoes.forEach(b => { 
    let over = (mouseX >= b.x && mouseX <= b.x + 280 && mouseY >= b.y && mouseY <= b.y + 45); 
    stroke(over ? color(0, 255, 204) : 50); 
    strokeWeight(2); fill(over ? color(20, 25, 40) : 15); 
    rect(b.x, b.y, 280, 45, 6);
  
    noStroke(); fill(over ? color(0, 255, 204) : 200); textSize(14); text(b.nome, b.x + 140, b.y + 22);
    
    if (over && mouseIsPressed) { 
      ritmoEscolhido = b.ritmo; 
      multiplicadorRitmo = b.mult; estado = "JOGANDO"; mouseIsPressed = false; 
    } 
  }); 
  textAlign(LEFT, BASELINE); 
}
function desenharCenario() { 
  stroke(0, 255, 204, 30); 
  strokeWeight(2); 
  for(let i = 0; i < 5; i++) { line(0, 200 + i * 22, width, 200 + i * 22); }
  stroke(0, 255, 204, 150); 
  strokeWeight(4); 
  line(0, 450, width, 450);
  
  stroke(0, 255, 204, 40); 
  strokeWeight(1); 
  for (let x = 0; x < width; x += 25) { line(x, 450, x - 10, 500);} 
}

function proximoBoss(index) {
// 🔥 troca música dependendo do boss
let indexMusica = bossAtual % 4;
tocarMusica(indexMusica);
  // ✅ SE NÃO FOR O PRIMEIRO BOSS (SOL)
  if (index > 0) {

    if (random() < 0.20) { // 20% chance
      mostrarJumpScare = 120; // 2 segundos (60fps)

      // 🔥 volta para o início
      bossAtual = 0;
      index = 0;
    }
  }

  if (index >= dadosBosses.length) {
    estado = "VITORIA";
    return;
  }

  chefao = new Chefao(dadosBosses[index]);

  tirosBoss = [];
  textosDano = [];
  minions = [];
  barrasCompasso = [];

  tempoBoss = 0;

  if (jogador) jogador.resetarFase();
}

function atualizarJogo() { 
  tempoBoss++; 
  tempoBarra++;

// a cada 10 segundos (600 frames)
if (!repeticaoAtiva) {
  if (tempoBarra % 600 === 0) {
    if (random() < 0.01) {
      barrasCompasso.push(new BarraCompasso());
    }
  }
}
  jogador.atualizar(); 
  if (chefao) chefao.atualizar();
  // SPAWN DE MINIONS
if (tempoBoss % 600 === 0) {
  if (random() < 0.30) {
    let quantidadeMinions = floor(random(1, 6));

    for (let m = 0; m < quantidadeMinions; m++) {
      minions.push(new Minion());
    }
  }
}
  // SPAWN DE BARRAS (independente)
if (tempoBoss % 1000 === 0) {
  if (random() < 0.05) {
    barrasCompasso.push(new BarraCompasso());
  }
}

// ATUALIZAÇÃO DAS BARRAS
for (let i = barrasCompasso.length - 1; i >= 0; i--) {
  barrasCompasso[i].atualizar();

  if (barrasCompasso[i].foraDaTela()) {
    barrasCompasso.splice(i, 1);
  }
}
  
  // ADICIONADO: Atualização e colisão dos Minions 
  for (let i = minions.length - 1; i >= 0; i--) { minions[i].atualizar();
  // Colisão do Minion com o jogador
if (minions[i].colideCom(jogador)) { 
  if (jogador.dashIframe > 0) { 
    // Ignorado por causa do I-Frame 
  } else if (!jogador.emDash) { 
      jogador.receberDano(1); 
    } 
  } 
  if (minions[i].morto) { 
    minions.splice(i, 1); 
  } 
}
for (let i = particulas.length - 1; i >= 0; i--) { particulas[i].atualizar(); 
  if (particulas[i].morto()) particulas.splice(i, 1); 
}
for (let i = textosDano.length - 1; i >= 0; i--) { 
textosDano[i].atualizar(); 
if (textosDano[i].morto()) textosDano.splice(i, 1); 
}
  
for (let i = tirosjogador.length - 1; i >= 0; i--) { tirosjogador[i].atualizar(); 
let tiroDestruido = false;
  // ADICIONADO: Colisão do tiro do jogador com Minions
 for (let m = minions.length - 1; m >= 0; m--) { 
   if (tirosjogador[i] && 
  tirosjogador[i].colideCom(minions[m])) { minions[m].receberDano(1); 
gerarExplosao(tirosjogador[i].x, tirosjogador[i].y, color(180, 0, 255), 4); 
 tirosjogador.splice(i, 1); tiroDestruido = true; break; 
   } 
 }  
                                                    
  if (tiroDestruido) continue;                                                  
if (tirosjogador[i].foraDaTela()) { tirosjogador.splice(i, 1); } 
else if (chefao && tirosjogador[i].colideCom(chefao)) { 
chefao.receberDano(1); let corPart = color(240, 180, 0);
if(ritmoEscolhido === "4/4") corPart = color(255, 215, 0);
if(ritmoEscolhido === "6/8") corPart = color(0, 255, 255);
if(ritmoEscolhido === "3/4") corPart = color(0, 255, 120); 
if(ritmoEscolhido === "2/4") corPart = color(255, 0, 255);
if(ritmoEscolhido === "c") corPart = color(120, 200, 255);
if(ritmoEscolhido === "12/8") corPart = color(225, 0, 0);
if(ritmoEscolhido === "9/8") corPart = color(255, 120, 0);
gerarExplosao(tirosjogador[i].x, tirosjogador[i].y, corPart, 6);
tirosjogador.splice(i, 1);
  } 
 }  
  for (let i = tirosBoss.length - 1; i >= 0; i--) {
    tirosBoss[i].atualizar();
    if (tirosBoss[i].foraDaTela()) { tirosBoss.splice(i, 1); }
    else if (tirosBoss[i].colideCom(jogador)) {
      // ADICIONADO: Ativação do Parry caso seja nota rosa E o jogador esteja na subida do pulo duplo
    if (tirosBoss[i].isParry && jogador.pulosRestantes === 0 && jogador.vy < 0) {
 jogador.cartas = min(jogador.maxCartas, jogador.cartas + 1);
    textosDano.push(new TextoFlutuante(jogador.x, jogador.y - 20, "PERFECT PARRY!", color(255, 20, 147))); gerarExplosao(tirosBoss[i].x, tirosBoss[i].y, color(255, 20, 147), 25);
  tremorTela = max(tremorTela, 8); 
    tirosBoss.splice(i, 1);
      } else if (jogador.dashIframe > 0) { 
  // Ignora o projétil completamente sem sofrer dano (Graças aos 0.90s de I-Frame do Dash) 
    tirosBoss.splice(i, 1); 
      } else if (!jogador.emDash) { 
  jogador.receberDano(1); 
    tirosBoss.splice(i, 1); 
      } 
    } 
  } 
}
function renderizarJogo() { 
   particulas.forEach(p => p.desenhar());
  minions.forEach(m => m.desenhar());
  jogador.desenhar(); 
  barrasCompasso.forEach(b => b.desenhar()); // ✅ só aqui
  if (chefao) chefao.desenhar(); 
  tirosjogador.forEach(t => t.desenhar()); 
  tirosBoss.forEach(t => t.desenhar()); 
  textosDano.forEach(t => t.desenhar()); 

}
function desenharInterface() { 
  fill(15, 15, 25, 230); stroke(0, 255, 204, 100); rect(15, 15, 280, 80, 5); 
  fill(255); noStroke(); textSize(11); text("COMANDOS: ESPAÇO (PULO/PARRY DUPLO) | F (DASH IFRAME)", 25, 32);
  fill(0, 255, 204);
  text(`RITMO SELECIONADO: ${ritmoEscolhido}`,25, 48);
for(let i=0;i<jogador.maxHp; i++){
  fill(i < jogador.hp ? color(255, 50, 50) : color(40));
rect(25 + i * 22, 60, 16, 12, 2);
}
for(let i=0; i<jogador.maxCartas; i++) {
  fill(i < jogador.cartas ? color(255, 20, 147) : color(40));
  rect(150 + i * 18, 60, 12, 16, 1);
}
if (chefao) {
fill(15, 15, 25, 230);
stroke(chefao.cor);
rect(width - 285, 15, 270, 55, 5);
fill(255);
noStroke();
textSize(11);
text(chefao.nome, width - 275, 32);                      
fill(40);
rect(width - 275, 42, 250, 12, 3);
fill(chefao.cor);
rect(width - 275, 42, map(max(0, chefao.hp), 0, chefao.maxHp, 0,250), 12, 3);
   }
}

function telaFimDeJogo() {
  fill(5, 5, 10, 240);
  rect(0, 0, width, height);
  fill(255);
  textSize(36);
  textAlign(CENTER, CENTER);
  text(estado === "VITORIA" ? "👑 MAESTRO SUPREMO!" : "❌ FORA DO TEMPO", width / 2, height / 2 - 20);
  textSize(16);
  fill(0, 255, 204);
  text("Pressione R para recompor os compassos", width / 2, height / 2 + 30);
  textAlign(LEFT, BASELINE);
}

function keyPressed() {

  // 🔥 RESET com R (funciona fora do jogo)
  if (key === 'r' || key === 'R') {
    estado = "MENU";
    bossAtual = 0;
    tempoBoss = 0;
    tirosBoss = [];
    tirosjogador = [];
    minions = [];
    barrasCompasso = [];
    textosDano = [];
    particulas = [];

    if (jogador) jogador.resetarFase();

    return; // 🔥 impede de continuar
  }

  // 🔒 bloqueia resto se não estiver jogando
  if (estado !== "JOGANDO") return;

  if (key === ' ') {
    jogador.pular();
  }

  if (key === 'f' || key === 'F') {
    jogador.dispararDash();
  }

  if (key === 'z' || key === 'Z') {
    jogador.tiroAutomatico = !jogador.tiroAutomatico;
  }

  if (key === 'v' || key === 'V') {
    jogador.soltarEspecial();
  }
  if (key === 'p' || key === 'P') {
  if (estado === "JOGANDO") estado = "PAUSADO";
  else if (estado === "PAUSADO") estado = "JOGANDO";
}
if (key === 'p' || key === 'P') {
  if (estado === "JOGANDO") estado = "PAUSADO";
  else if (estado === "PAUSADO") estado = "JOGANDO";
}
if (key === 'm' || key === 'M') {
  let proxima = (musicaAtual + 1) % 4;
  tocarMusica(proxima);
 }
}


function gerarExplosao(x, y, cor, qtd) {
  for(let i=0; i<qtd; i++) particulas.push(new Particula(x, y, cor, "faisca"));
}
function desenharQuartefusa(x, y) {
  push();

  stroke(220, 220, 235);
  strokeWeight(2);

  // cabeça
  fill(220, 220, 235);
  ellipse(x, y, 10, 7);

  // haste
  line(x + 5, y, x + 5, y - 30);

  // 5 colchetes
  for (let i = 0; i < 5; i++) {
    let offset = i * 5;
    line(x + 5, y - 30 + offset, x + 12, y - 25 + offset);
  }

  pop();
}
// --- CLASSES REESTRUTURADAS ---
// ADICIONADO: Nova classe para instanciar Minions no ecossistema musical
class BarraCompasso {
  constructor() {
    this.x = width + 20;
    this.vel = 6 * multiplicadorRitmo;
    this.largura = 5; // margem de colisão
    this.jaDeuDano = false;
  }

  atualizar() {
    this.x -= this.vel;

    // colisão com jogador (executa 1 vez)
 if (
  !this.jaDeuDano &&
  this.x > jogador.x &&
  this.x < jogador.x + jogador.w &&
  jogador.dashIframe <= 0 &&
  jogador.invul <= 0
) {
  jogador.receberDano(1);
  this.jaDeuDano = true;
}
    
  }

  desenhar() {
    stroke(255);
    strokeWeight(2);
    line(this.x, 0, this.x, height);
  }

  foraDaTela() {
    return this.x < -20;
  }
}

class Minion {
  constructor() {
    this.x = width + random(20, 100);
    this.y = random(220, 420);
    this.w = 32;
    this.h = 32;
    this.vx = random(-2.5, -4.5) * multiplicadorRitmo;
    this.hp = 1;
    this.morto = false;
    this.icone = chefao ? chefao.icone : "♩";
    this.cor = chefao ? chefao.cor : "#12f323";
  }

  receberDano(qtd) {
    this.hp -= qtd;
    if (this.hp <= 0) this.morto = true;
  }

  atualizar() {// ✅corrigido
    this.x += this.vx;
    this.y += sin(tempoBoss * 0.08 + this.x) * 1.2;
    if (this.x < -40) this.morto = true;
  }

  colideCom(obj) {
    return (
      this.x + this.w > obj.x &&
      this.x < obj.x + obj.w &&
      this.y + this.h > obj.y &&
      this.y < obj.y + obj.h
    );
  }

  desenhar() {// ✅ corrigido
    push();
    textAlign(CENTER, CENTER);
    textSize(36);
    fill(this.cor);
    noStroke();
    text(this.icone, this.x + this.w/2, this.y + this.h/2);
    pop();
  }
}

class Player {
  constructor() { //ADICIONADO:Hitbox reduzida drasticamente de 36x60 para 24x24 lógicos para máxima precisão e controle
    this.x = 100;
    this.y = 390;
    this.w = 24;
    this.h = 24;
    this.vx = 0;
    this.vy = 0;
    this.velocidade = 6.2;
    this.gravidade = 0.7;
    this.forcaPulo = -13.5;
    this.hp = 4;
    this.maxHp = 4;
    this.ultimoTiro = 0;
    this.delayTiro = 110;
    this.emDash = false;
    this.direcaoDash = 1;
    this.tempoDash = 0;
    this.duracaoDash = 12;
    this.velocidadDash = 21;
    this.cooldownDash = 0;
    this.cartas = 0;
    this.maxCartas = 5;
    this.ultimoEspecial = 0;
    this.delayEspecial = 200;
    this.tiroAutomatico = false;
    this.mutilado = false;
    this.pulosRestantes = 2;
    this.invul = 0;
    this.dashIframe = 0;//ADICIONADO: Contador lógico para os 0.90s de imunidade do Dash
  }
  resetarFase() { this.mutilado = false; this.hp = this.maxHp; this.h = 24; this.invul = 0; this.dashIframe = 0;}
  pular() { 
    if (this.pulosRestantes > 0) { 
this.vy = this.forcaPulo; this.pulosRestantes--; 
    let corFum = this.pulosRestantes === 0 ? 
color(255, 20, 147, 200) : color(0, 255, 204, 150); for(let i=0; i<8; i++) particulas.push(new Particula(this.x+this.w/2, this.y+this.h, corFum, "fumaça")); 
    } 
  }
  dispararDash() { 
    if (this.cooldownDash === 0) { 
      this.emDash = true; 
      this.tempoDash = this.duracaoDash; 
      this.cooldownDash = 30; 
      this.dashIframe = 54; // ADICIONADO: 54 Frames = Exatos 0.90 Segundos de Invulnerabilidade Total 
      this.vy = 0; 
      tremorTela = max(tremorTela, 4); 
    }
  }
  soltarEspecial() {
    if (!chefao || millis() - this.ultimoEspecial < this.delayEspecial) return; 
    if (this.cartas === 5) { 
      chefao.receberDano(chefao.maxHp * 0.5); 
      this.cartas = 0; 
      tirosBoss = []; 
      tremorTela = 35; 
      textosDano.push(new TextoFlutuante(chefao.x + 30, chefao.y, "SINFONIA FINALE!!", color(255, 0, 0))); 
      gerarExplosao(chefao.x + chefao.w/2, chefao.y + chefao.h/2, color(255, 0, 0), 50);
    }
    else if (this.cartas >= 1) { 
      chefao.receberDano(20); 
      this.cartas--; tremorTela = 12; textosDano.push(new TextoFlutuante(chefao.x + 30, chefao.y, "CORDAS ACELERADAS -20", color(255, 120, 0))); 
      gerarExplosao(chefao.x + chefao.w/2, chefao.y + chefao.h/2, color(255, 120, 0), 20);
    } 
    this.ultimoEspecial = millis(); 
  }
  receberDano(qtd) { 
    if (this.invul > 0 || this.emDash || this.dashIframe > 0) return; 
    this.hp -= qtd; this.invul = 50; tremorTela = 18; 
    textosDano.push(new TextoFlutuante(this.x, this.y, "-1 HP", color(255, 0, 0))); 
    if (this.hp <= 0) estado = "DERROTA"; 
  }
  atualizar() { 
    if (this.cooldownDash > 0) this.cooldownDash--; 
    if (this.invul > 0) this.invul--; 
    if (this.dashIframe > 0) this.dashIframe--; 
    // Decrementa a janela de tempo do I-Frame
    
    if (this.emDash) { 
      this.vx = this.velocidadDash * this.direcaoDash; 
      let corTrail = color(0, 255, 204, 180); 
      if (this.dashIframe > 0) corTrail = color(255, 255, 255, 220);
      // Brilha branco durante imunidade 
      particulas.push(new Particula(this.x + this.w/2, this.y + random(0, this.h), corTrail, "rastro")); 
      this.tempoDash--; 
      if (this.tempoDash <= 0) this.emDash = false; 
    } else {
      if (keyIsDown(65)) { this.vx = -this.velocidade; this.direcaoDash = -1; } else if (keyIsDown(68)) { this.vx = this.velocidade; this.direcaoDash = 1; } else { this.vx = lerp(this.vx, 0, 0.25); 
      } 
      // Movimentação mais fluida e amortecida 
      this.vy += this.gravidade; 
    } 
    this.x = constrain(this.x + this.vx, 0, width - this.w); 
    this.y += this.vy; 
    let chao = 450 - this.h; 
    if (this.y >= chao) { 
      if(this.vy > 2) { for(let i=0; i<3; i++) 
        particulas.push(new Particula(this.x+this.w/2, chao+this.h, color(0, 255, 204, 100), "fumaça")); 
 }
      this.y = chao; 
      this.vy = 0; 
      this.pulosRestantes = 2; 
    } 
    if (this.tiroAutomatico && millis() - this.ultimoTiro > this.delayTiro) { 
      tirosjogador.push(new Tiro(this.x + (this.direcaoDash === 1 ? this.w : -10), this.y + this.h/2, this.direcaoDash * 15)); 
      this.ultimoTiro = millis();
    } 
  }
  desenhar() { 
    if (this.invul % 6 > 3) return; // if (this.invul % 6 > 3) return;
    push(); 
    if (imgjogador) { 
      image(imgjogador, this.x, this.y, this.w, this.h); 
    } else { 
      // Desenho gráfico de alta definição: Núcleo cibernético brilhante baseado na hitbox reduzida 
      stroke(0, 255, 204); 
      strokeWeight(this.dashIframe > 0 ? 3 : 1.5); 
      fill(this.dashIframe > 0 ? 255 : color(0, 255, 204));
      rect(this.x, this.y, this.w, this.h, 4); 
      // Aura visual externa fluida simulando o tamanho antigo para preservar a elegância estática
      noStroke(); 
      fill(0, 255, 204, 40); 
      ellipse(this.x + this.w/2, this.y + this.h/2, 38, 38); 
    } pop(); 
  } 
}
class Chefao { 
  constructor(dados) { 
    this.nome = dados.nome;
    this.hp = dados.hp; 
    this.maxHp = dados.hp;
    this.tipo = dados.tipo; 
    this.cor = dados.cor; 
    this.icone = dados.icone; 
    this.x = 620; this.y = 240; this.w = 95; 
    this.h = 145; 
    this.bravo = false; 
    this.flashHit = 0; 
    this.faseMorrendo = false;
    this.cooldownPausa = 0;
  }

  receberDano(qtd) { 
    this.hp -= qtd; 
    this.flashHit = 4; 

    if (this.hp <= this.maxHp * 0.5) this.bravo = true; 
    if (this.hp <= this.maxHp * 0.25) this.faseMorrendo = true; 

    if (this.hp <= 0) {

      const BOSSES_SEM_REPETICAO = ["sol", "fa", "do", "percussão"];
let proximoIndice = bossAtual + 1;
repetirFase = false;

// 🔥 Só bosses permitidos podem repetir
if (
  !BOSSES_SEM_REPETICAO.includes(this.tipo.trim()) &&
  random() < 0.01 &&
  !repeticaoAtiva
) {
  repetirFase = true;
  repeticaoAtiva = true;

  // efeito visual
  textosDano.push(
    new TextoFlutuante(this.x, this.y, ":||", color(255))
  );
}
      setTimeout(() => {
if (repetirFase) {
  bossAtual = 0; // 🔥 volta pro começo (clave de sol)
} else {
  bossAtual = proximoIndice;
}
        proximoBoss(bossAtual);

      }, 800);
    }
  }

  atualizar() { 
    if (this.flashHit > 0) this.flashHit--; 

    if (this.faseMorrendo) { 
      this.x += random(-6, 6); 
      this.y += random(-3, 3); 
      this.x = constrain(this.x, 500, width - this.w);
    } else { 
      let alvoY = 240 + sin(tempoBoss * 0.06) * 35; 
      this.y = lerp(this.y, alvoY, 0.1); 
      this.x += sin(tempoBoss * 0.05) * (this.bravo ? 4.5 : 2.0); 
    } 

    let cadencia = this.bravo ? 13 : 24;
    if (this.tipo === "quimera_final") cadencia = this.bravo ? 6 : 9;

    if (tempoBoss % cadencia === 0) this.dispararNota();
  }

  dispararNota() { 
    let nTipo = "♩";
    let tNota = 28; 
    let isParry = (tempoBoss % 4 === 0);

    let pausaTipos = {
      pausa_breve: "𝄺",
      pausa_semibreve: "𝄻",
      pausa_minima: "𝄼",
      pausa_seminima: "𝄽",
      pausa_colcheia: "𝄾",
      pausa_semicolcheia: "𝄿",
      pausa_fusa: "𝅀",
      pausa_semifusa: "𝅁"
    };

    let chanceParry = 0.60;
    let isParryPausa = random() < chanceParry;

    let simbolo = pausaTipos[this.tipo] || "";
    let corPausa = isParryPausa ? color(255, 20, 147) : this.cor;

    textosDano.push(
      new TextoFlutuante(this.x + this.w/2, this.y, simbolo, corPausa)
    );

    // 🔵 PAUSAS
    if (this.tipo.startsWith("pausa")) {

      if (this.cooldownPausa > 0) {
        this.cooldownPausa--;
        return;
      }

      this.cooldownPausa = 60;

      if (this.tipo === "pausa_breve") {
        for (let i = 0; i < 5; i++) minions.push(new Minion());
      }

      else if (this.tipo === "pausa_semibreve") {
        tirosBoss = [];
        setTimeout(() => {
          for (let i = 0; i < 8; i++) {
            tirosBoss.push(new NotaMusical(this.x, random(200,400), -6, "♩", 30, false));
          }
        }, 400);
      }

      else if (this.tipo === "pausa_minima") {
        tremorTela = 10;
      }

      else if (this.tipo === "pausa_seminima") {
        tirosBoss.push(new NotaMusical(this.x, jogador.y, -10, "♩", 25, false));
      }

      else if (this.tipo === "pausa_colcheia") {
        for (let i = 0; i < 2; i++) {
          tirosBoss.push(new NotaMusical(this.x, random(200,400), -12, "𝅘𝅥𝅮", 22, isParryPausa));
        }
      }

      else if (this.tipo === "pausa_semicolcheia") {
        for (let i = 0; i < 3; i++) {
          tirosBoss.push(new NotaMusical(this.x, random(200,400), -14, "𝅘𝅥𝅯", 20, false));
        }
      }

      else if (this.tipo === "pausa_fusa") {
        for (let i = 0; i < 5; i++) {
          tirosBoss.push(new NotaMusical(this.x, random(200,400), -16, "𝅘𝅥𝅰", 18, false));
        }
      }

      else if (this.tipo === "pausa_semifusa") {
        for (let i = 0; i < 4; i++) {
          tirosBoss.push(new NotaMusical(this.x, random(200,400), -20, "𝅘𝅥𝅲", 16, false));
        }
      }

      return;
    }

    // 🔵 BOSSES NORMAIS
    if (this.tipo === "sol") nTipo = random() > 0.4 ? "♩" : "𝅘𝅥𝅮";

    else if (this.tipo === "fa") { 
      nTipo = "𝅗𝅥"; 
      tNota = 38; 
    }

    else if (this.tipo === "minima_seminima") {
      nTipo = random() > 0.5 ? "𝅗𝅥" : "♩";
    }

    else if (this.tipo === "colcheia_semicolcheia") {
      nTipo = random() > 0.5 ? "𝅘𝅥𝅮" : "♩";
    }

    else if (this.tipo === "semifusa") {
      nTipo = random() > 0.5 ? "𝅘𝅥𝅰" : "𝅿";
    }

    else if (this.tipo === "quartefusa") {
      for (let i = 0; i < 6; i++) {
        tirosBoss.push(
          new NotaMusical(
            this.x,
            random(200,400),
            -22,
            "quartefusa",
            20,
            random() < 0.3
          )
        );
      }
      return;
    }

    else if (this.tipo === "quimera_final") { 
      let todas = [
        "𝅜","𝅝","𝅗𝅥","♩","𝅘𝅥𝅮","𝅘𝅥𝅯","𝅘𝅥𝅰","𝅘𝅥𝅲",
        "𝄺","𝄻","𝄼","𝄽","𝄾","𝄿","𝅀","𝅁"
      ];

      nTipo = random(todas);

      if (ehPausa(nTipo)) {
        textosDano.push(
          new TextoFlutuante(this.x, this.y, nTipo, color(180,180,255))
        );
        return;
      }

      tNota = (nTipo === "𝅝" || nTipo === "𝅗𝅥") ? 42 : 25;
    }

    // 🔵 DISPARO FINAL
    let vel = (VEL_NOTAS[nTipo] || 5) * multiplicadorRitmo;

    if (jogador && jogador.x > this.x + this.w / 2) {
      vel = Math.abs(vel);
    } else {
      vel = -Math.abs(vel);
    }

    let yAlvo = lerp(this.y + this.h/2, jogador.y + jogador.h/2, random(0.4, 0.95));

    tirosBoss.push(
      new NotaMusical(this.x + this.w/2, yAlvo, vel, nTipo, tNota, isParry)
    );
  }

  desenhar() { 
    push(); 

    if (imgBoss) { 
      image(imgBoss, this.x, this.y, this.w, this.h); 
    } else { 
      textAlign(CENTER, CENTER);
      textSize(this.tipo === "quimera_final" ? 170 : 140); 

      if (this.flashHit > 0) fill(255); 
      else fill(this.bravo ? color(255, 60, 60) : color(this.cor)); 

      noStroke(); 
      text(this.icone, this.x + this.w/2, this.y + this.h/2);

      if (random() > 0.75) { 
        particulas.push(
          new Particula(
            this.x + random(0, this.w),
            this.y + random(0, this.h),
            color(this.cor),
            "faisca"
          )
        );
      } 
    } 

    pop();
  } 
}
class Tiro { 
  constructor(x, y, vx) { 
    this.x = x; 
    this.y = y; 
    this.vx = vx; 
    this.w = 18; 
    this.h = 4; 
  } 
  atualizar() { this.x += this.vx; } 
  foraDaTela() { return this.x > width || this.x < 0; 
   } colideCom(obj) { return (this.x + this.w > obj.x && this.x < obj.x + obj.w && this.y + this.h > obj.y && this.y < obj.y + obj.h); 
                    } 
  desenhar() { 
    push(); 
let cTip = color(255, 200, 0);
    if (ritmoEscolhido === "12/8") cTip = color(255,50,0);
    if (ritmoEscolhido === "9/8") cTip = color(255, 120, 0); 
    if (ritmoEscolhido === "6/8") cTip = color(0, 255, 255); 
    if (ritmoEscolhido === "3/4") cTip = color(0, 255, 120);
    if (ritmoEscolhido === "2/4") cTip = color(255, 0, 255);
    if (ritmoEscolhido === "c") cTip = color(120, 200 ,255);
    // Renderização volumétrica do projétil de rastro neon 
    for(let i = 0; i < this.h; i++) { 
      stroke(red(cTip), green(cTip) - (i * 25), blue(cTip), 200); line(this.x, this.y + i, this.x + this.w, this.y + i); 
    } pop();
  } 
}
class NotaMusical { 
  constructor(x, y, vx, tipo, tamanho, isParry) { 
    this.x = x; 
    this.y = y; 
    this.vx = vx; 
    this.tipo = tipo; 
    this.tamanho = tamanho; 
    this.isParry = isParry;
  } 

  atualizar() { 
    this.x += this.vx; 
  } 

  foraDaTela() { 
    return this.x < -60 || this.x > width + 60; 
  } 

  // Ajustado para não sumir imediatamente ao ir para trás
  colideCom(obj) {
    return (
      this.x + this.tamanho * 0.35 > obj.x &&
      this.x < obj.x + obj.w &&
      this.y > obj.y &&
      this.y - this.tamanho * 0.75 < obj.y + obj.h
    ); 
  }

  desenhar() { 
    push(); 
    if (this.tipo === "quartefusa") {
  desenharQuartefusa(this.x, this.y);
  return;
}

    if (this.isParry) { 
      fill(255, 20, 147); 
      stroke(255); 
      strokeWeight(2.5); 
      // Brilho de parry expandido
    } else { 
      fill(220, 220, 235); 
      noStroke(); 
    } 

    textSize(this.tamanho); 
    text(this.tipo, this.x, this.y); 

    pop(); 
  } 
}
class Particula { 
  constructor(x, y, color, tipo) { 
    this.x = x; this.y = y; this.cor = color; 
    this.tipo = tipo; 
    this.vx = random(-3, 3); 
    this.vy = random(-3, 3); 
    this.alfa = 255; 
    this.tamanho = random(3, 7); 
    if(this.tipo === "rastro") { this.vx = 0; this.vy = 0; this.tamanho = 10; } 
    if(this.tipo === "fumaça") { this.vy = random(-1.2, -0.4); 
this.vx = random(-1.5, 1.5); 
this.tamanho = random(6, 14);
                               } 
  } atualizar() { 
    this.x += this.vx; 
    this.y += this.vy; 
    this.alfa -= this.tipo === "rastro" ? 24 : 9; 
  } 
  morto() { return this.alfa <= 0;
          } 
  desenhar() { 
    push(); 
    noStroke(); 
    let c = color(this.cor); c.setAlpha(this.alfa); 
    fill(c); 
    if (this.tipo === "fumaça") { ellipse(this.x, this.y, this.tamanho); 
} else { 
rect(this.x, this.y, this.tamanho, this.tamanho); 
         } 
    pop(); 
  } 
}
class TextoFlutuante {
  constructor(x, y, txt, cor) { this.x = x; 
  this.y = y; 
this.txt = txt; 
this.cor = cor; 
this.alfa = 255; 
this.vy = -1;
                              } 
  atualizar() { 
    this.y += this.vy; 
    this.alfa -= 6; 
  } 
  morto() { return this.alfa <= 0;
          } 
  desenhar() { 
    push(); 
    fill(red(this.cor), green(this.cor), blue(this.cor), this.alfa); 
    textSize(14); 
    textStyle(BOLD); 
    text(this.txt, this.x, this.y);
    pop(); 
  } 
}
