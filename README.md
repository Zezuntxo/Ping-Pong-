# Trabalho Prático 01

#### Pong em MonoGame com C# 
  
##### Engenharia e Desenvolvimento de Jogos Digitais - Técnicas de Desenvolvimento de Videojogos 
###### Gabriel Rosas nº27943 / Ricardo Costa nº27927 / João Reis 27917 

# __Indíce__
1. [__Introdução__](#Introdução)
2. [__Procedimentos do Jogo__](#procedimentos)
3. [__Implementação__](#Implementação)
4. [__Interpretação do Código-Fonte__](#analise)
5. [__Conclusão__](#Conclusão)

# __Introdução__

Neste trabalho o professor propos fazer a análise de um jogo à nossa escolha em monogame c# e apresentá-lo à turma. O objetivo é analisar e interpretar o jogo desde os códigos, às imagens e sons, ao processo criativo em destaque à logica e à estrutura do mesmo e às diversas ferramentas que os donos dos jogos utilizaram.
Pong é um dos primeiros e mais emblemáticos videojogos, lançado pela Atari em 1972. Dois jogadores controlam raquetes para rebater uma bola virtual, tentando superar o adversário. Sua mecânica simples e envolvente, combinada com gráficos minimalistas e sons eletrônicos, criou uma experiência cativante e nostálgica. "Pong" inspirou inúmeras variações e continua a influenciar os desenvolvedores de jogos hoje, sendo um marco na história dos videojogos.

<p align="center">
 <img src="https://raw.githubusercontent.com/MonoGame/MonoGame.Samples/3.8.1/Images/AutoPong_1.gif"  alt="Pong" width=700>
</p>



<a name="procedimentos"></a>
# __Procedimentos do Jogo__

* __Objetivo:__
    - O primeiro jogador a pontuar três vezes ganha;
* __Controlos:__
    - __Setas superior e inferior:__ Move a barra para cima e para baixo de forma a evitar que a bola saia dos limites do campo;
* __Score:__
    - A pontuação é composta por um melhor de 5.
      
# __Implementação__

## __Estrutura de Pastas:__

* __Pong:__
    - __Content:__
      * Sounds
      * SpriteSheets
     
<p align="center">
 <img src="https://github.com/hirako2000/MonoPingPong/blob/master/PongContent/PongContentContent/PongBall.png"  alt="Ball" width=15 hspace="20" >
 <img src="https://github.com/hirako2000/MonoPingPong/blob/master/PongContent/PongContentContent/PongBar.png"  alt="Ball" width=20 hspace="20" >
</p>

 __PongContent:__ É uma pasta com os spritesheets e sons do jogo.
      
* __Code:__
    - [ Program.cs](#program)
    - [ Bar.cs](#bar)
    - [ Ball.cs](#ball)  
    - [ CpuController.cs](#cpucontroller)
    - [ Game1.cs](#game1)
    - [ Score.cs](#score)
    - [ BallOutManager.cs](#balloutmanager)
      
 __Code:__ É uma pasta com o código-fonte do jogo.

<a name="analise"></a>
# __Interpretação do Código-Fonte__

<a name="program"></a>
## 	__Program.cs:__
Este código é resposável pela estrutura básica para iniciar o jogo Pong através da criação da classe Game1 e apenas a executa caso seja compilado em Windows ou Linux. Ou seja, este código é um exemplo básico de um ponto de entrada para uma aplicação de jogo (um jogo de Pong, neste caso) escrita em C#. Ele começa por definir uma classe principal Program com um método Main, que inicia o jogo com a criação de uma static class Game1 e depois executa o jogo. O atributo [STAThread] refere que a aplicação deve ser executada em um único thread de aplicação com um modelo de apartamento de thread COM. O bloco de código using é utilizado para garantir que a static class Game1 seja libertada após ser executada.







```
#region Using Statements
using System;
#endregion

namespace Pong {
#if WINDOWS || LINUX
    /// <summary>
    /// The main class.
    /// </summary>
    public static class Program
    {
        /// <summary>
        /// The main entry point for the application.
        /// </summary>
        [STAThread]
        static void Main()
        {
            using (var game = new Game1())
                game.Run();
        }
    }
#endif
```

<a name="bar"></a>
## 	__Bar.cs:__
É composto por uma classe representa uma barra de jogo no Pong, que inclui métodos para mover a barra, controlar a velocidade da mesma e obter informações sobre a sua posição e textura.
```
using System.Runtime.InteropServices;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Graphics;

namespace Pong {
    class Bar {


        private readonly SoundEffect soundEffect;
        private readonly Texture2D barTexture;

        private readonly int worldHeight;

        private readonly int xPosition;
        private int yPosition;
        private int previousYposition;

        public const int Height = 80;
        public const int Width = 10;

        private const int InitialMovementLength = 3;
        private const int MaxMovementLength = 10;
        private int movementLength = InitialMovementLength;

        public Bar(Texture2D texture, int xPosition, int worldHeight, SoundEffect soundEffect)
        {
            this.soundEffect = soundEffect;
            this.worldHeight = worldHeight;
            barTexture = texture;
            this.xPosition = xPosition - Width / 2;
            this.yPosition = worldHeight/2 - Height/2;

            this.previousYposition = this.yPosition;
        }

        public void IncreaseMovementSpeed()
        {
            if (movementLength > MaxMovementLength) {
                // Set some limit
                return;
            }
            movementLength = movementLength + InitialMovementLength;
        }

        public void ResetHorizontalMovementSpeed()
        {
            movementLength = InitialMovementLength;
        }

        public int GetXposition()
        {
            return xPosition;
        }

        public int GetYposition()
        {
            return yPosition;
        }

        public void MoveUp() {
            previousYposition = yPosition;
            if (yPosition > 0)
            {
                yPosition = yPosition - movementLength;
            }
            
        }

        public void MoveDown() {
            previousYposition = yPosition;
            if (yPosition < worldHeight - Height)
            {
                yPosition = yPosition + movementLength;
            }
        }

        public Texture2D GetTexture() {
            return barTexture;
        }

        public int GetPreviousYposition()
        {
            return previousYposition;
        }

        public int YCenterOfBar()
        {
            return yPosition + Height/2;
        }

        public void PlaySound() {
            soundEffect.Play();
        }
    }
}
```

<a name="ball"></a>
## 	__Ball.cs:__
O ball.cs define a lógica básica de uma bola no jogo Pong, incluindo o movimento, as colisões e as interações com as barras dos jogadores.

```
using System;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Graphics;

namespace Pong {


    class Ball
    {

        private readonly SoundEffect soundEffect;

        private const int BarHeight = 80;
        private const int BarWidth = 10;
        private const int TopAndBottomPadding = 20;

        private readonly Texture2D texture;

        private readonly int worldHeight;
        private readonly int worldWidth;

        private int xPosition;
        private int yPosition;

        private int verticalMovement = 0;
        private int horizontalMovement = 5;

        private int previousXposition;

        public Ball(Texture2D texture, int xPosition, int worldWidth, int worldHeight, SoundEffect soundEffect)
        {
            this.soundEffect = soundEffect;
            this.texture = texture;
            this.xPosition = xPosition;
            this.yPosition = worldHeight / 2;
            this.worldHeight = worldHeight;
            this.worldWidth = worldWidth;

            this.previousXposition = xPosition;

        }

        public void IncreasetHorizontalMovement() {
            if (Math.Abs(horizontalMovement) > 10) {
                return;
            }

            if (horizontalMovement > 0) {
                horizontalMovement = horizontalMovement + 1;
            }
            else {
                horizontalMovement = horizontalMovement - 1;
            }
        }

        public void ResetHorizontalMovement()
        {
            horizontalMovement = 5;
        }

        public Texture2D GetTexture()
        {
            return texture;
        }

        public int GetXposition()
        {
            return xPosition;
        }

        public int GetYposition()
        {
            return yPosition;
        }

        public void Move()
        {
            previousXposition = xPosition;
            MoveVertically();
            MoveHorizontally();
        }

        public void MoveToCenter() {
            xPosition = this.yPosition = worldHeight / 2;
            BounceHorizontal();
            verticalMovement = 0;
        }

        private void MoveHorizontally()
        {
            if (xPosition > worldWidth || xPosition < 0)
            {
                BounceHorizontal();
            }

           xPosition = xPosition + horizontalMovement;
        }

        private void MoveVertically()
        {
            if (yPosition > worldHeight - TopAndBottomPadding || yPosition < 0 + TopAndBottomPadding)
            {
                BounceVertical();
                PlayEdgeSound();
            }
  

            yPosition = yPosition + verticalMovement;
        }

        public void CheckHit(Bar playerBar, Bar cpuBar)
        {
            if(IsCollision(playerBar))
            {
                Swing(playerBar);
                BounceHorizontal();
                playerBar.PlaySound();

                playerBar.IncreaseMovementSpeed();

            }

            if(IsCollision(cpuBar)) {
                Swing(cpuBar);
                BounceHorizontal();
                cpuBar.PlaySound();

                cpuBar.IncreaseMovementSpeed();
            }
        }

        private void Swing(Bar bar)
        {
            verticalMovement = OffCollision(bar);
            IncreasetHorizontalMovement();
        }

        private void PlayEdgeSound() {
            soundEffect.Play();
        }

        private Boolean IsCollision(Bar bar)
        {

            return IsXcollision(bar) && IsYcollision(bar);
        }

        private Boolean IsXcollision(Bar bar)
        {
            return xPosition <= bar.GetXposition() + BarWidth && xPosition >= bar.GetXposition();
        }

        private int OffCollision(Bar bar)
        {
            // That's some interesting bit, how should the ball bounce when colliding with the bar??
            // Let's make it simple: the further away from the middle of the bar, the least horizontally the ball will go 
            var diff = yPosition - bar.YCenterOfBar();
            var off = (diff) / 2;
            return off;
        }

        public Boolean IsYcollision(Bar bar) {
            return yPosition < bar.GetYposition() + BarHeight && yPosition > bar.GetYposition();
        }


        private void BounceVertical()
        {
            verticalMovement = verticalMovement * -1;
        }

        private void BounceHorizontal() {
            horizontalMovement = horizontalMovement * -1;
        }

        public Boolean IsGoingRight()
        {
            return previousXposition < xPosition;
        }
    }
}
```

<a name="cpucontroller"></a>
## 	__CpuController.cs:__
Cpucontrolle.cs controla o comportamento da barra, controlada pelo computador no jogo Pong, movimentando a barra para mais perto da posição vertical da bola quando necessário e também ajusta-se para o centro do campo, se necessário.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Pong {
    class CpuController
    {

        private readonly Bar cpuBar;
        private readonly Ball ball;
        private readonly int worldHeight;
        private const int MovementLength = 5;

        private const int BarHeight = 80;

        private const int ProximityTolerance = 3;

        public CpuController(Bar cpuBar, Ball ball, int worldHeight)
        {
            this.cpuBar = cpuBar;
            this.ball = ball;
            this.worldHeight = worldHeight;
        }

        public void UpdatePosition()
        {
            MoveCloseToBall();
        }

        public void MoveCloseToBall()
        {
            if (Math.Abs(ball.GetYposition() - cpuBar.YCenterOfBar()) > ProximityTolerance) ;
            {
                MoveCloserTo(ball.GetYposition());
            }
     
        }

        private void AdjustToCenter()
        {
            if (!ball.IsGoingRight())
            {
                MoveCloserTo(worldHeight / 2);
            }
        }

        private void MoveCloserTo(int point)
        {
            var middleOfBar = cpuBar.GetYposition() + BarHeight/2;
            if (middleOfBar < point)
            {
                cpuBar.MoveDown();
            }
            if (middleOfBar > point)
            {
                cpuBar.MoveUp(); ;
            }
        }


    }
}
```

<a name="game1"></a>
## 	__Game1.cs:__
Esta classe controla o ciclo de vida do jogo Pong. Atualiza a lógica e desenha o campo, a bola, as barras, e o score com as devidas dimensões na tela.
```
#region Using Statements
using System;
using System.Globalization;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
#endregion

namespace Pong {

    public class Game1 : Game {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;

        private const int Height = 600;
        private const int Width = 800;

        private const int BarToEdgePadding = 40;

        private Bar playerBar;
        private Bar cpuBar;
        private BallOutMananger ballOutManager;

        private CpuController cpuController;
        private Score score;

        public Game1(): base() 
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
            this.IsMouseVisible = true;

            graphics.PreferredBackBufferHeight = Height;
            graphics.PreferredBackBufferWidth = Width;
        }

        /// <summary>
        /// Allows the game to perform any initialization it needs to before starting to run.
        /// This is where it can query for any required services and load any non-graphic
        /// related content.  Calling base.Initialize will enumerate through any components
        /// and initialize them as well.
        /// </summary>
        protected override void Initialize() {
            base.Initialize();
        }

        /// <summary>
        /// LoadContent will be called once per game and is the place to load
        /// all of your content.
        /// </summary>
        protected override void LoadContent() {
            // Create a new SpriteBatch, which can be used to draw textures.
            spriteBatch = new SpriteBatch(GraphicsDevice);

            Content.RootDirectory = "Content";
            var barTexture = Content.Load<Texture2D>("PongBar");
            var ballTexture = Content.Load<Texture2D>("PongBall");

            var ballOutSound = Content.Load<SoundEffect>("ballOutSound");
            var ballCollideWithBarSound = Content.Load<SoundEffect>("ballBarCollision");
            var ballCollideWithEdgeSound = Content.Load<SoundEffect>("ballEdgeCollision");

            var music = Content.Load<SoundEffect>("music");
            SoundEffectInstance soundEffectInstance = music.CreateInstance();
            soundEffectInstance.IsLooped = true;
            soundEffectInstance.Play();

            var font = Content.Load<SpriteFont>("Score");

            playerBar = new Bar(barTexture, BarToEdgePadding, Height, ballCollideWithBarSound);
            cpuBar = new Bar(barTexture, Width - BarToEdgePadding, Height, ballCollideWithBarSound);

            var ball = new Ball(ballTexture, Width / 2, Width, Height, ballCollideWithEdgeSound);

            score = new Score(font);
            ballOutManager = new BallOutMananger(ball, Width, ballOutSound, score);

            cpuController = new CpuController(cpuBar, ball, Height);

        }


        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// all content.
        /// </summary>
        protected override void UnloadContent() {
            // TODO: Unload any non ContentManager content
        }

        /// <summary>
        /// Allows the game to run logic such as updating the world,
        /// checking for collisions, gathering input, and playing audio.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Update(GameTime gameTime) {
            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed || Keyboard.GetState().IsKeyDown(Keys.Escape))
                Exit();


            if (score.IsGameFinished()) {
                if (GamePad.GetState(PlayerIndex.One).Buttons.Start == ButtonState.Pressed || Keyboard.GetState().IsKeyDown(Keys.Enter)) {
                    score.Reset();
                    playerBar.ResetHorizontalMovementSpeed();
                    cpuBar.ResetHorizontalMovementSpeed();
                }
                return;
            }

            var keyboardState = Keyboard.GetState();
            var gamePadState = GamePad.GetState(PlayerIndex.One);
            if (keyboardState.IsKeyDown(Keys.Up) || gamePadState.DPad.Up == ButtonState.Pressed)
            {
                playerBar.MoveUp();
            }

            if (keyboardState.IsKeyDown(Keys.Down) || gamePadState.DPad.Down == ButtonState.Pressed)
            {
               playerBar.MoveDown();
            }

            if (ballOutManager.IsBallOut()) {
                ballOutManager.ResetBallAfterLatency();
                ballOutManager.GetBall().ResetHorizontalMovement();
                playerBar.ResetHorizontalMovementSpeed();
                cpuBar.ResetHorizontalMovementSpeed();
            }
            else 
            {
                ballOutManager.Move();
                ballOutManager.GetBall().CheckHit(playerBar, cpuBar);
                cpuController.UpdatePosition(); 
            }
            base.Update(gameTime);
        }

        private void GameOver()
        {
            DisplayEndMessage("Game Over");
        }

        private void Win()
        {
            DisplayEndMessage("You  Win");
        }

        private void DisplayEndMessage(String message)
        {
            spriteBatch.DrawString(score.GetFont(), message, new Vector2(40, Height / 2), Color.White, 0f, new Vector2(0, 0), new Vector2(3, 3), SpriteEffects.None, 0f);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime) {
            GraphicsDevice.Clear(Color.Black);

            spriteBatch.Begin();

            // Draw player and cpu bars
            spriteBatch.Draw(playerBar.GetTexture(), new Rectangle(playerBar.GetXposition(), playerBar.GetYposition(), Bar.Width, Bar.Height), Color.White);
            spriteBatch.Draw(cpuBar.GetTexture(), new Rectangle(cpuBar.GetXposition(), cpuBar.GetYposition(), Bar.Width, Bar.Height), Color.White);

            // Draw the ball
            spriteBatch.Draw(ballOutManager.GetBall().GetTexture(), new Rectangle(ballOutManager.GetBall().GetXposition(), ballOutManager.GetBall().GetYposition(), 8, 8), Color.White);

            // Draw the Scrore values
            spriteBatch.DrawString(score.GetFont(), score.GetPlayerScore().ToString(CultureInfo.InvariantCulture), new Vector2(Width / 2 - 40 - 15, 20), Color.White);
            spriteBatch.DrawString(score.GetFont(), score.GetCpuScore().ToString(CultureInfo.InvariantCulture), new Vector2(Width / 2 + 40, 20), Color.White);

            DrawNet();

            if (score.PlayerWin())
            {
                Win();
            }

            if (score.CpuWin())
            {
                GameOver();
            }

            spriteBatch.End();


            base.Draw(gameTime);
        }

        private void DrawNet()
        {
            for (var yPosition = 20; yPosition < Height - BarToEdgePadding; yPosition += 35)
            {
                spriteBatch.Draw(playerBar.GetTexture(), new Rectangle(Width / 2, yPosition, 5, 30), Color.White);
            }
        }
    }
}
```

<a name="score"></a>
## 	__Score.cs:__
Score.cs é responsável por gerir as pontuações do jogo Pong. Além disso, fornece métodos para manipular a pontuação, verificar o fim do jogo e determinar o vencedor.

```
using System;
using Microsoft.Xna.Framework.Graphics;

namespace Pong {

    class Score {

        private int playerScore = 0;
        private int cpuScore = 0;
        private readonly SpriteFont font;

        private const int ScoreToWin = 9;

        public Score(SpriteFont font)
        {
            this.font = font;
        }

        public SpriteFont GetFont()
        {
            return font;
        }

        public void IncrementPlayerScore()
        {
            this.playerScore++;
        }

        public void IncrementCpuScore() {
            this.cpuScore++;
        }

        public int GetPlayerScore()
        {
            return this.playerScore;
        }

        public int GetCpuScore()
        {
            return this.cpuScore;
        }

        public Boolean IsGameFinished()
        {
            return this.playerScore >= ScoreToWin || this.cpuScore >= ScoreToWin;
        }

        public void Reset()
        {
            this.cpuScore = 0;
            this.playerScore = 0;
        }

        public Boolean PlayerWin()
        {
            return this.playerScore >= ScoreToWin;
        }

        public Boolean CpuWin() {
            return this.cpuScore >= ScoreToWin;
        }
    }
}
```

<a name="balloutmanager"></a>
## 	__BallOutManager.cs:__
Esta classe observa o comportamento da bola quando ela sai dos limites do campo. Por outro lado, reposiciona a bola, faz a devida contagem dos pontos e reproduz os efeitos sonoros.
```
#region Using Statements
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
#endregion


namespace Pong {
    class BallOutMananger
    {

        private readonly SoundEffect soundEffect;

        private readonly Ball ball;
        private readonly int worldWidth;

        private const int ResetBallLatencyInFrames = 60;
        private const int LeftAndRightPadding = 10;

        private int latchDelay = 0;

        private readonly Score score;

        public BallOutMananger(Ball ball, int worldWidth, SoundEffect soundEffect, Score score)
        {
            this.score = score;
            this.ball = ball;
            this.worldWidth = worldWidth;
            this.soundEffect = soundEffect;
        }

        public void Move()
        {
            ball.Move();   
        }

        public void ResetBallAfterLatency()
        {
            if (latchDelay == 1) {
                ball.MoveToCenter();
            }
            else 
            {
                PlayBallOutSound();
                initDelay();
            }

            latchDelay--;
        }

        private void initDelay()
        {
            if (latchDelay == 0)
            {
                latchDelay = ResetBallLatencyInFrames;
            }
            
        }

        public Boolean IsBallOut()
        {
            if (IsOutLeft())
            {
                IncrementCpuScore();
                return true;

            }
            if (IsOutRight())
            {
                IncrementPlayerSCore();
                return true;
            }
            return false;
        }

        private void IncrementCpuScore()
        {
            if (latchDelay == 0)
            {
                score.IncrementCpuScore();
            }
        }

        private void IncrementPlayerSCore()
        {
            if (latchDelay == 0)
            {
                score.IncrementPlayerScore();
            }
        }

        private Boolean IsOutRight()
        {
            return ball.GetXposition() > worldWidth - LeftAndRightPadding;
        }

        private Boolean IsOutLeft()
        {
            return ball.GetXposition() < LeftAndRightPadding;

        }

        private void PlayBallOutSound()
        {
            if (latchDelay == 60 -1)
            {
                soundEffect.Play();
            }
            
        }

        private void PlayBallBackInSound()
        {
            
        }

        public Ball GetBall()
        {
            return ball;
        }

    }
}
```
# __Conclusão:__
A análise detalhada do código do jogo Pong foi muito esclarecedora. Permitiu-nos compreender profundamente como este jogo funciona por dentro, desde a gestão das pontuações até ao movimento das barras e da bola. Esta análise revelou-se importante para identificar áreas de melhoria e adaptação futuras. Em resumo, proporcionou-nos uma visão completa e clara do funcionamento interno do jogo Pong.
<p align="center">
 <img src="https://i.pinimg.com/564x/41/29/cb/4129cbe2260f5d72002da94a83de4b0c.jpg"  alt="PongPc" width=300>
</p>
