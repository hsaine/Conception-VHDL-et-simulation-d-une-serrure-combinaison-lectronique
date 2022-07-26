<h1 align='center'>Conception VHDL et simulation d'une serrure à combinaison électronique.</h1>

 <p align="center"> <img src="serrure.JPG" ></p> 

## Définition de VHDL

* Le langage de description matérielle VHSIC est un langage de description matérielle qui peut modéliser le comportement et la structure des systèmes numériques à plusieurs niveaux d'abstraction, allant du niveau système jusqu'à celui des portes logiques, à des fins de saisie de conception, de documentation et de vérification.
* L’abréviation VHDL signifie VHSIC Hardware Description Langage (VHSIC : Very High Speed Integrated Circuit). Ce langage a été écrit dans les années 70 pour fournir un langage de haut niveau adapté à la description fonctionnelle des systèmes complexes.


## Objectifs
* L’objectif premier de ce projet est de modéliser en langage VHDL un circuit de serrure à combinaison électronique, et puis de valider la description élaborée en procédant à une simulation sur le logiciel Modelsim. 
* Ce projet sera une occasion pour faire un retour sur les approches de descriptions fournies par le langage VHDL, ainsi que pour développer une bonne maitrise des différentes structures algorithmiques qui sont à la base de ces approches. 
* Chaque description réalisée doit être obligatoirement accompagnée d’un banc de test qui la valide.
* Chaque composant, avant qu’il soit modélisé, nécessite une recherche approfondie autour de son fonctionnement. 

## INTRODUCTION

une serrure est un mécanisme fonctionnant avec une clé, permettant de bloquer une porte ou une ouverture en position fermée.
Le projet consiste à réaliser une serrure qui composent d'un(e):
* Codeur : son rôle est de traduire le code généré par le clavier et transporté par **le bus data_in** en code binaire sur 4-bits qui sera envoyé au système via le **bus data_s**.
* Détecteur :son rôle est de détecter l’appui sur une touche et positionner **le signal detect à 1**. Si aucune pression sur une touche n’est détectée , **le signal de sortie est mis à 0**.
* Mémoire :son rôle est de stocker le code saisi par l’utilisateur. Elle est constituée de **4 registres lecture/écriture parallèles 4-bits actifs
sur front montant**. Quand le signal Detect passe à l'état haut, la valeur du signal présent sur la sortie du codeur (data_s) est stockée dans le premier registre et les valeurs précédentes contenues dans les autres registres sont décalées d'un registre. Le bus code_sig reçoit donc les valeurs des quatre registres. Les 4 premiers bits de ce bus représentent la valeur du premier registre qui correspondent au chiffre de la touche enfoncée en dernier.
* Comparateur : son rôle est de détecter l’égalité de deux valeurs codés sur **16 bits en entrée**. La clef de la serrure qui fera la base
de comparaison (fixed_key) est fixé à 1234. Lorsque le code entré par l'utilisateur est égal au code de la clef, le signal ouverture passe à 1, sinon le signal ouverture reste à 0. Pour élaborer la description VHDL du comparateur 16-bits, il faut tout d’abord réaliser la description d’un comparateur 4-bits et puis faire le câblage nécessaire

## Présentation du circuit de la serrure 
Une serrure à combinaison électronique nécessite la composition d’un code confidentiel
(chiffres et/ou lettres) pour être déverrouillée. Dans le cas de la serrure qui fera l’objet de ce
projet, le code est constitué de 4 chiffres qu’il faut taper sur un clavier de 16 touches
numériques connectées à la serrure. Chaque touche est connectée au codeur de la serrure
via un fils du bus de données binaires (data_in) de 16 bits. Les connexions avec le codeur
sont montrées sur la figure suivante :

 <p align="center"> <img src="figure.JPG" ></p> 


Tous les fils du bus sont au niveau bas tant qu'aucune touche n'est encore pressée. L'appui
sur une des touches met le fil qui la relie au codeur au niveau haut tandis que les autres fils
restent au niveau bas. La table suivante montre l’état de bus de données lors de l'appui sur
les différentes touches du clavier.
![Tableau!](tableau.JPG)

## L’architecture globale du circuit de la serrure
L’architecture globale du circuit de la serrure est représentée sur la figure suivante :
 <p align="center"> <img src="architecture.JPG" ></p> 
 
## Code De projet 
1. Explication

| Composant      | Role | Explication     |
| :----:      |    :----:   |        :----: |
| Codeur      | Le rôle de codeur est de traduire le code généré par le clavier et transporté par le bus data_in en code binaire sur 4-bits qui sera envoyé au  système via le bus data_s.       | Le Codeur a une entrée Data_in codé sur 16 Bits et une sortie Data_s sur un 4 Bits. Chaque touche de la serrure est code sur 16 Bits alors quand l'utilisateur appui sur une touche Data_in va reçoit son code en binaire et selon le process chaque touche va transformé en 4 bits selon les transformations du tableau ci-dessus  |
| Bascule |    |   Pour pouvoir modéliser en VHDL la mémoire, il faut Faire la description d'une bascule D modifiée active sur front montant, possédant un signal reset « RST » qui remettra à zéro de façon asynchrone la sortie Q, et une entrée Enable(detect) qui active le transfert de l’entrée vers la sortie s’il est à 1. la bascule a quatre entrés (D,CLK,RST,enable)de type Std logic et une seule sortie (Q)de type std_logique.RST: Signal qui remet à zéro d'une façon asynchrone la sortie Q.clock : pour savoir si la bascule D est active sur front montant. enable: Signal qui active le transfert de l’entrée vers la sortie s’il est à 1. |
| Registre |    |   Pour pouvoir modéliser la mémoire, il faut aussi Réaliser un registre à Lecture/Ecriture parallèle 4 bits, à base de bascules D modifiées actives sur front montant.   |
|  Mémoire|   Son rôle est de stocker le code saisi par l’utilisateur. Elle est constituée de 4 registres lecture/écriture parallèles 4-bits actifs sur front montant |  Quand le signal Detect passe à l'état haut, la valeur du signal présent sur la sortie du codeur (data_s) est stockée dans le premier registre et les valeurs précédentes contenues dans les autres registres sont décalées d'un registre. Le bus code_sig reçoit donc les valeurs des quatre registres. Les 4 premiers bits de ce bus représentent la valeur du premier registre qui correspondent au chiffre de la touche enfoncée en dernier.|
| COMPARATEUR 4 BITS |Le rôle est de comparateur de 4bits est de détecter l’égalité de deux valeurs codés sur 4 bits    | Le comparateur contient deux entrés A,B codé sur 4 bitset une sortie EQU sur 1 bits.on a passé à la liste de sensibilité les deux entres A et Bla sortie sera 1 les deux nombres sont égaux     |
|COMPARATEUR 16 BITS  |Le rôle est de détecter l’égalité de deux valeurs codés sur 16bits en entrée. La clef de la serrure qui fera la base de comparaison (fixed_key) est fixé à 1234.    | Lorsque le code entré par l'utilisateur est égal au code de la clef, le signal ouverture passe à 1, sinon le signal ouverture reste à 0  |
| SERRURE |    | pour modéliser toute la serrure :on fait l'appel de tous les composants le codeur, le détecteur, la mémoire et le comparateur l'instanciation de chaque composant liaison les entrés et les sorties des composants aves des signaux internes DATA_S detect codesig et les signaux externe Data-in et open |

2. Code De la serrure 

```
LIBRARY IEEE;
USE IEEE.STD_LOGIC_1164.ALL;
USE IEEE.STD_LOGIC_UNSIGNED.ALL;

Entity Serrure is --entité
port(
DATA_in : in std_logic_vector(15 downto 0);
CLK : in std_logic;
RST : in std_logic;
openn : out std_logic);
End Serrure ;
Architecture ARCS of Serrure is -- architecture
component detecteur is
port(
Data_in: in std_logic_vector(15 downto 0);
Detect: out std_logic);
end component;
component Memoire
port(
DATA_s : in std_logic_vector(3 downto 0);
clk,RST,enable :in std_logic;
code_sig : out std_logic_vector(15 downto 0));
end component;

component cmprtr16b is
port( code_sig: in  std_logic_vector(15 downto 0);

          Openn : out  std_logic );
end component;

component codeur is
port(
Data_in: in std_logic_vector(15 downto 0);
Data_s: out std_logic_vector(3 downto 0));
end component;


--Signal CLK,RST: std_logic;
Signal Detect: std_logic ;

Signal Data_s : std_logic_vector(3 downto 0);
signal code_sig : std_logic_vector(15 downto 0);

Begin

 inst_detecteur: entity work.detecteur(ARCHI_detecteur) port map(Data_in,Detect);
 inst_codeur: entity work.codeur(ARCHI_codeur) port map(Data_in ,Data_s);
 inst_memoire: entity work.Memoire(ARC1) port map(Data_s,CLK ,RST,Detect,code_sig);

 inst_comparateur: entity work.cmprtr16b(arc16) port map(code_sig,openn);

End ARCS ;

```

3. Banc de test du serrure


```
LIBRARY IEEE;
USE IEEE.STD_LOGIC_1164.ALL;
USE IEEE.STD_LOGIC_UNSIGNED.ALL;

Entity Serrure_tb is --entité

End Serrure_tb ;
Architecture ARCS_tb of Serrure_tb is -- architecture

component Serrure
port(
DATA_in : in std_logic_vector(15 downto 0);
CLK : in std_logic;
RST : in std_logic;
openn : out std_logic);
End component;

Signal CLK,RST : std_logic;
--Signal Detect : std_logic ;
--Signal Data_s,data_s1 : std_logic_vector(3 downto 0);
--signal code_sig : std_logic_vector(15 downto 0);
signal Data_in :  std_logic_vector(15 downto 0);
signal openn :  std_logic;
Begin
inst:Serrure port map(DATA_in,CLK,RST ,openn);
--Detect_process :process is
--begin
  -- Detect <= '1';
   --wait for 200 ns;
    -- Detect <= '0';
    --wait for 1000 ns;
  
--end process;

clock_process :process is
begin
     CLK <= '0';
     wait for 10 ns;
     CLK <= '1';
     wait for 10 ns;
  
end process;

process
begin
RST <= '0';
DATA_IN <= "0000000000000001";
wait for 20 ns;
DATA_IN <= "0000000000000010";
wait for 20 ns;
DATA_IN <= "0000000000000100";
wait for 20 ns;
DATA_IN <= "0000000000010000";
wait for 20 ns;

RST <= '1';
wait for 10 ns;
RST <= '0';

wait for 10 ns;
RST <= '0';
DATA_IN <= "0000000000010000";
wait for 20 ns;
DATA_IN <= "0000000000000100";
wait for 20 ns;
DATA_IN <= "0000000000000010";
wait for 20 ns;
DATA_IN <= "0000000000000001";
wait for 20 ns;
end process;

End ARCS_tb ;
```
4. Simulation du serrure

![simulation!](simulation.JPG)

5. le code des autres composantes 
<p>Pour le code de codeur, detecteur,memoire,registre et comparateur veuillez me contacter. </p>

## CONCLUSION

Au terme de ce rapport qui présente les détails d’un projet d’enrichissement, on a effectué ce projet de VHDL pour la réalisation d'une serrure, en tant que des Etudiants en cycle d'ingénieur au sein de l'université Euromed de Fès. Lors de ce projet, on a pu mettre en pratique nos connaissances théoriques acquises durant notre formation à l'université. On signale que Ce projet s’est révélé très enrichissant dans la mesure où il a consisté en une approche concrète du métier d’ingénieur. En effet, la prise d’initiative, le respect des délais.
