# Trabalho Prático - Pong em MonoGame com C# - Engenharia e Desenvolvimento de Jogos Digitais - Gabriel Rosas nº27943 / Ricardo Costa nº27927 / João Reis 27919 

# __Indíce__
1. [__Introdução__](#Introdução)
2. [__Implementação__](#Implementação)
3. [__Instruções de Jogo__](#instru)
4. [__Decisões de Implementação__](#decisoes)
5. [__Análise dos Códigos Disponibilizados__](#analise)
6. [__Conclusão__](#Conclusão)

# __Introdução__


<p align="center">
 <img src="https://raw.githubusercontent.com/MonoGame/MonoGame.Samples/3.8.1/Images/AutoPong_1.gif"  alt="Pong" width=700>
</p>


  
"Pong" é um dos primeiros videojogos, um clássico intemporal que inaugurou uma era de entretenimento digital. Desenvolvido pela Atari e lançado em 1972, este jogo simples, mas cativante, conquistou milhões de jogadores em todo o mundo.
A mecânica de "Pong" é direta e envolvente. Dois jogadores controlam cada um uma raquete no ecrã, movendo-a para cima e para baixo para rebater uma bola virtual. O objetivo é simples: fazer a bola passar pela raquete do adversário para marcar pontos. Parece fácil, mas a velocidade da bola aumenta à medida que o jogo avança, exigindo reflexos rápidos e precisão dos jogadores.
O ambiente minimalista de "Pong", com os seus gráficos simples e sons eletrónicos, transporta os jogadores para uma atmosfera nostálgica. A simplicidade do design é parte do seu charme intemporal, tornando-o acessível e viciante para jogadores de todas as idades.
Desde o seu lançamento, "Pong" inspirou inúmeras variações e continua a ser uma fonte de inspiração para os desenvolvedores de jogos até hoje. A sua influência é evidente em muitos dos títulos modernos, mesmo aqueles que exploram territórios completamente diferentes.
Para concluir "Pong" é mais do que um simples jogo. É um marco na história dos videojogos, um símbolo de uma era em que a diversão digital estava apenas a começar. A sua simplicidade, jogabilidade viciante e legado duradouro garantem que continuará a ser lembrado e apreciado por gerações futuras.

# __Implementação__

## __Estrutura de Pastas:__

* __Pong/__
    - |-- Content/
    - |-- Fonts/
    - |-- SpriteSheets/
    - |-- Sounds/
      
* __Code/__
    - [|-- Program.cs](#program)
    - [|-- Bar.cs](#bar)
    - [|-- Ball.cs](#ball)  
    - [|-- CpuController.cs](#cpucontroller)
    - [|-- Game1.cs](#game1)
    - [|-- Score.cs](#score)
    - [|-- Menu.cs](#menu)
    - [|-- BallOutManager.cs](#balloutmanager)



<a name="program"></a>
## 	__Program.cs:__



