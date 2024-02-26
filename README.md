
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Orientación ideológica de los Partidos Políticos en Uruguay

*Nicolás Schmidt*

> ***Resumen:*** El documento que sigue explora desde la ciencia
> política las distintas maneras que se han utilizado para identificar
> la orientación ideológica de los partidos políticos. Luego de detectar
> posibles problemas con las mediciones actuales se provee una manera
> alternativa utilizada en la literatura, pero, hasta ahora, no
> utilizada para el caso uruguayo. Se implementa un escalamiento
> multidimensional para reconstruir el espacio ideológico de los
> partidos políticos a partir de votaciones nominales y de los discursos
> parlamentarios de los legisladores. En este documento se detalla el
> paso a paso de las decisiones metodológicas y la explicación del
> código elaborado para cada etapa del análisis: construcción de los
> datos, visualización, modelado y análisis.

<!-- badges: start -->
<!-- badges: end -->

### Índice

- [Introducción](#introducción)
- [Construcción de los datos](#construcción-de-los-datos)
  - [Paquete `speech`](#paquete-speech)
    - [Extracción de texto](#extracción-de-texto)
    - [Extracción de votaciones
      nominales](#extracción-de-votaciones-nominales)
  - [Simulación de votaciones
    nominales](#simulación-de-votaciones-nominales)
- [Modelos de escalamiento](#modelos-de-escalamiento)
- [Análisis y visualización](#análisis-y-visualización)
- [Discusión](#discusión)
- [Referencias](#referencias)
- [Notas](#notas)

## Introducción

La indagación en torno a la orientación ideológica de los partidos
políticos en Uruguay ha permeado de manera implícita gran parte de la
producción académica en el ámbito de las ciencias sociales en dicho
país. En el contexto de una democracia multipartidista como la uruguaya,
estos actores han ocupado un lugar central en las explicaciones de
diversos fenómenos políticos, sociales y económicos. A pesar de su
destacada relevancia, existe escasa evidencia sistemática que aborde de
manera exhaustiva el posicionamiento ideológico de los partidos a lo
largo de la historia. En este sentido, emergen dos concepciones
convencionales respecto a la orientación ideológica de los partidos
uruguayos: la estabilidad y la moderación. Resulta notable la ausencia
de investigaciones que ofrezcan una evidencia sistemática, ya sea de
índole cualitativa o cuantitativa, a lo largo del tiempo. Por ende, la
estabilidad y moderación ideológica en Uruguay han sido sustentadas en
evidencia circunstancial y por ende potencialmente sesgada.

La interrogante que surge naturalmente es: ¿Cómo es posible reconstruir
la orientación ideológica de los partidos a lo largo del tiempo? Se han
desarrollado diversas técnicas con este propósito, entre las cuales se
destacan:

- Encuestas a expertos.
- Encuestas de élites.
- Análisis de programas partidarios (Comparative Manifestos Project).
- Análisis cuantitativo de textos.
- Estimación de puntos ideales a través de métodos de escalamiento.

Cada una de estas metodologías presenta fortalezas y debilidades
específicas. Si bien existen antecedentes de la aplicación de varias de
estas técnicas en investigaciones sobre los partidos uruguayos, los
resultados obtenidos han generado hallazgos significativos. No obstante,
es crucial reconocer que la evidencia resultante es fragmentaria,
incompleta y en algunos casos, no concluyente.

En este contexto, este trabajo se propone trazar el camino de la
reconstrucción de la orientación ideológica de los partidos políticos
uruguayos a lo largo de un siglo de historia. A través de técnicas de
escalamiento, nuestro trabajo se enfoca en la reconstrucción del
posicionamiento ideológico de todos los partidos políticos uruguayos con
representación parlamentaria entre los años 1911 y 2010. Este enfoque
nos permite superar las limitaciones de la evidencia circunstancial
previa y contribuir de manera significativa al entendimiento más
profundo y sistemático de la dinámica ideológica de los partidos en el
contexto político uruguayo.

La forma de llevar adelante esta tarea es a través de las votaciones
nominales en el congreso. Como veremos, esto representa un desafío para
el caso uruguayo. A los efectos de avanzar en la construcción de
evidencia adicional, además de testear distintos modelos, vamos a
cotejar con evidencia alternativa. Se va a llevar adelante un
escalamiento a partir de los discursos parlamentarios y cotejar la
convergencia o divergencia entre el comportamiento a la hora de votar
como a la hora de hablar de los legisladores. El trabajo se estructura
en cuatro grandes secciones, una relativa a la construcción de los
datos, otra relativa al modelado de los datos, en tercer lugar, una
relativa al análisis y la visualización y por último un apartado de
discusión que permita vislumbrar los posibles aportes a esta agenda así
como los aspectos pendientes en los cuales se puede seguir avanzado.

## Construcción de los datos

Para poder llevar adelante la tarea propuesta es necesario contar con
dos fuentes de datos. En primer lugar, con las votaciones de los
legisladores en el congreso, y en segundo lugar el texto de lo que
exponen en cada sesión.

El parlamento uruguayo no cuenta con un registro de las votaciones que
los legisladores realizan en sala. Es decir, no hay un registro nominal
de las votaciones. La constitución uruguaya prevé dos situaciones en las
que se debe registrar nominalmente las
votaciones.<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>
En primer lugar, un legislador cualquiera puede solicitar que la
votación de un asunto se haga nominal, lo cual implica una votación no
nominal para resolver la viabilidad de la propuesta y luego se lleva
adelante la votación nominal si es que la propuesta tuvo como resultado
una votación afirmativa. El segundo momento en donde la votación debe
ser nominal es cuando el congreso debe votar vetos interpuestos por el
poder ejecutivo.

De este modo, las votaciones nominales del primer tipo suelen ser
erráticas y con un componente no sistemático ya que comúnmente son
asuntos que para alguno de los legisladores (particularmente diputados)
son de relevancia para su electorado y quiere dejar constancia de su
voto de forma nominal. Las votaciones nominales de los vetos tienen un
defecto y una virtud. La virtud es que son asuntos que potencialmente
dejan en evidencia las posiciones de los distintos actores (recordemos
que un veto sucede cuando el Poder Ejecutivo rechaza en su totalidad o
en parte un proyecto de ley sancionado por el congreso). A los efectos
del objetivo del presente trabajo resulta ser una buena selección que la
totalidad de los vetos estén registrados nominalmente. La dificultad que
tiene el uso de estas votaciones nominales es que la democracia uruguaya
no se caracteriza por utilizar esta potestad de forma recurrente. Es
decir, los conflictos (si es que los hay) son dirimidos en instancias
previas.

Una dificultad adicional a esto es que no existe un registro sistemático
de la cantidad de vetos y por ende de la fecha en la que sucedieron.
Esto tiene como implicación que la única manera de acceder a esta
información es recurriendo a los diarios de sesión del congreso. Al
mismo tiempo, los diarios no están digitalizados en su totalidad.

Frente a esta dificultad el objetivo fue trabajar con los diarios de
sesión del congreso de una manera sistemática que permita procesar todos
los diarios de sesión de una manera conjunta, replicable y reproducible.

A tales efectos en conjunto con otros colegas hemos desarrollado un
paquete de R que permite procesar y extraer la información necesaria
para poder tener los datos para escalar. El paquete de R `speech`
implementa un algoritmo de recuperación de datos que permite hacer dos
cosas:

1.  Detectar y extraer votaciones nominales
2.  Extraer únicamente el texto de la sesión dicho por cada legislador

### Paquete `speech`

La estructura de datos que necesitamos es la siguiente. Para cada
votación nominal existente, necesitamos tener el resultado de esta.
Adicionalmente a esto vamos a necesitar el discurso que fundamenta el
voto. Esto da como resultado que la unidad es un legislador en una
votación nominal. Si en una sesión hay dos votaciones nominales, ese
legislador va a aparecer como dos observaciones distintas.

#### Extracción de texto

Avancemos en la extracción del texto de los diarios de sesión. El
siguiente código permite extraer el texto de un documento .pdf en el que
está el registro de la sesión en donde cada fila es la intervención de
un legislador. El algoritmo creado permite detectar tanto la
distribución a dos columnas como el momento en el que un legislador
particular va a hacer uso de la palabra. EL resultado esperado es que
cada fila de la matriz sea la intervención de un legislador en una
sesión. <sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>

En el siguiente código se muestra cómo es el proceso de extracción de un
diario y el resultado esperado:

``` r
library(speech)
url <- "https://parlamento.gub.uy/documentosyleyes/documentos/diarios-de-sesion/6084/IMG"
text <- speech::speech_build(file = url)
text

#> # A tibble: 24 × 7
#>    legislator speech                  chamber date       legislature id      sex
#>    <chr>      <chr>                   <chr>   <date>           <int> <chr> <dbl>
#>  1 BORDABERRY SEÑOR BORDABERRY. Pido… COMISI… 2019-09-17          48 2019…     1
#>  2 BORDABERRY SEÑOR BORDABERRY. Prop… COMISI… 2019-09-17          48 2019…     1
#>  3 AVIAGA     SEÑORA AVIAGA. Pido la… COMISI… 2019-09-17          48 2019…     0
#>  4 AVIAGA     SEÑORA AVIAGA. En el m… COMISI… 2019-09-17          48 2019…     0
#>  5 GOÑI       SEÑOR GOÑI. Pido la pa… COMISI… 2019-09-17          48 2019…     1
#>  6 GOÑI       SEÑOR GOÑI. El Frente … COMISI… 2019-09-17          48 2019…     1
#>  7 MAHIA      SEÑOR MAHIA. Pido la p… COMISI… 2019-09-17          48 2019…     1
#>  8 MAHIA      SEÑOR MAHIA. Gracias, … COMISI… 2019-09-17          48 2019…     1
#>  9 ABDALA     SEÑOR ABDALA. Voto por… COMISI… 2019-09-17          48 2019…     1
#> 10 ASTI       SEÑOR ASTI. Obviamente… COMISI… 2019-09-17          48 2019…     1
#> # ℹ 14 more rows
```

Para la extracción del texto de todos los diarios de sesión debemos
utilizar otra función del paquete que permite establecer las fechas
entre las que se desea buscar para obtener los enlaces a los diarios
para iniciar la extracción:

``` r
links <- speech_url(chamber = "A", days = c("1985-03-1", "2010-02-15"))
text <- speech::speech_build(links, compiler = T)
```

Una vez que tenemos el texto extraído debemos hacer un trabajo detallado
con los nombres de los legisladores ya que lo que queremos es juntar
todo el texto que dicen en una sesión, por lo que puede que en algún
lugar de la sesión el texto este dañado y no se recupere adecuadamente
el nombre de un legislador, y puede que al compilar queden como dos
legisladores cuando en realidad es uno solo. Para eso se elaboró una
función con un patrón genérico y con casos específicos para ejecutar
sobre el texto:

<details>
<summary>
<b><a style="cursor: pointer;">Ver código </a></b>
</summary>

``` r
parse_names <- function(x){

        x <- chartr('\u00e1\u00e9\u00ed\u00f3\u00fa','aeiou', x)
        o <- x
        for(i in 1:length(x)){

                if(x[i] == "Alfie, Issac"){o[i] <- "Alfie, Isaac"}
                if(x[i] == "Alonso, Nelson R."){o[i] <- "Alonso, Nelson Rene"}
                if(x[i] == "Amaral"){o[i] <- "Amaral, Mario Alberto"}
                if(x[i] == "Amaro, Juan J."){o[i] <- "Amaro Corrado, Juan Justo"}
                if(x[i] == "Amaro, Juan Justo"){o[i] <- "Amaro Corrado, Juan Justo"}
                if(x[i] == "Andrade Ambrosoni, Jorge"){o[i] <- "Andrade Ambrosi, Jorge"}
                if(x[i] == "Andrade Rodríguez, Néstor"){o[i] <- "Andrade Rodriguez, Nestor Homero"}
                if(x[i] == "Araujo, German"){o[i] <- "Araujo, Jose German"}
                if(x[i] == "Arrarte, Roberto"){o[i] <- "Arrarte Fernandez, Roberto"}
                if(x[i] == "Asiain, Roberto"){o[i] <- "Asiain Oliver, Roberto"}
                if(x[i] == "Assi, Alfredo"){o[i] <- "Asti, Alfredo"}
                if(x[i] == "Baliero"){o[i] <- "Baliero Silva, Washington Fredy"}
                if(x[i] == "Baron Arena, Hector"){o[i] <- "Baron Arena, Hector Nestor"}
                if(x[i] == "Barrios, Artigas"){o[i] <- "Barrios, Artigas A."}
                if(x[i] == "Belvisi, Walter"){o[i] <- "Belvisi, Walter M."}
                if(x[i] == "Bentancur"){o[i] <- "Bentancur Ferreira, Juan Alvario"}
                if(x[i] == "Bergenstein"){o[i] <- "Bergstein, Nahum"}
                if(x[i] == "Berois, Ricardo"){o[i] <- "Berois Quinteros, Ricardo"}
                if(x[i] == "Berro"){o[i] <- "Berro Rodriguez, Bernardo Pedro"}
                if(x[i] == "Berro, Berarrdo"){o[i] <- "Berro Rodriguez, Bernardo Pedro"}
                if(x[i] == "Blasina, Jose L."){o[i] <- "Blasina, Jose Luis"}
                if(x[i] == "Bonilla Suarez, Edgard"){o[i] <- "Bonilla Suarez, Edgar Antonio"}
                if(x[i] == "Bosch, Federico"){o[i] <- "Bosch, Juan Federico"}
                if(x[i] == "Brause Berreta, Alberto"){o[i] <- "Brause Berreta, Alberto Juan"}
                if(x[i] == "Bruno"){o[i] <- "Bruno, Jorge"}
                if(x[i] == "Cadenas Boix"){o[i] <- "Cadenas Boix, Enrique"}
                if(x[i] == "Capeche, cayetano"){o[i] <- "Capeche, Cayetano"}
                if(x[i] == "Capeche, Eugenio"){o[i] <- "Capeche, Pio Eugenio"}
                if(x[i] == "Caputi, Tabare"){o[i] <- "Caputi, Tabare Angel"}
                if(x[i] == "Carambula, marcos"){o[i] <- "Carambula, Marcos"}
                if(x[i] == "Carbone, Alvaro"){o[i] <- "Carbone Rico, Enrique Alvaro"}
                if(x[i] == "Cardozo, Julio"){o[i] <- "Cardozo Ferreira, Julio"}
                if(x[i] == "Carrera Sapriza"){o[i] <- "Carrere Sapriza, Justino"}
                if(x[i] == "Cassina, Carlos"){o[i] <- "Cassina, Carlos Alberto"}
                if(x[i] == "Castro Riera"){o[i] <- "Castro Riera, Omar"}
                if(x[i] == "Castro, Omar"){o[i] <- "Castro Riera, Omar"}
                if(x[i] == "Castroman, Ricardo"){o[i] <- "Castroman Rodriguez, Ricardo"}
                if(x[i] == "Cigliuti, Carlos W."){o[i] <- "Cigliuti, Carlos Walter"}
                if(x[i] == "Clavijo, Hebert"){o[i] <- "Clavijo, Heber"}
                if(x[i] == "Conde Montes  de Oca, Jorge"){o[i] <- "Conde Montes de Oca, Jorge"}
                if(x[i] == "Conde Montes De Oca, Jorge"){o[i] <- "Conde Montes de Oca, Jorge"}
                if(x[i] == "Cortazzo, Victor"){o[i] <- "Cortazzo, Victor M."}
                if(x[i] == "Croce"){o[i] <- "Croce Urbina, Juan Pablo"}
                if(x[i] == "Curbelo"){o[i] <- "Curbelo Cravanzola, Luis Alberto"}
                if(x[i] == "Curbelo, Luis Alberto"){o[i] <- "Curbelo Cravanzola, Luis Alberto"}
                if(x[i] == "Cusano, Mauricio"){o[i] <- "Cussano, Mauricio"}
                if(x[i] == "Da rosa"){o[i] <- "Da Rosa Vazquez, Eber"}
                if(x[i] == "Da Rosa Viñoles, Eber"){o[i] <- "Da Rosa Vazquez, Eber"}
                if(x[i] == "Da Rosa, Eber"){o[i] <- "Da Rosa Vazquez, Eber"}
                if(x[i] == "Darricarrere"){o[i] <- "Damboriarena, Luis A."}
                if(x[i] == "de Pazos Parada, Mario Hermes"){o[i] <- "De Pazos Parada, Mario Hermes"}
                if(x[i] == "de Posadas, Ignacio"){o[i] <- "De Posadas, Ignacio"}
                if(x[i] == "Delgado, Daniel H."){o[i] <- "Delgado, Daniel Hugo"}
                if(x[i] == "Diaz Chavez, Jose E."){o[i] <- "Diaz Chaves, Jose Enrique"}
                if(x[i] == "Diaz, Jose"){o[i] <- "Diaz, Jose E."}
                if(x[i] == "Diaz Ruben"){o[i] <- "Diaz, Ruben"}
                if(x[i] == "Dosetti"){o[i] <- "Dossetti Rodriguez, Gustavo Jose"}
                if(x[i] == "Dossetti"){o[i] <- "Dossetti Rodriguez, Gustavo Jose"}
                if(x[i] == "Elso Goñi"){o[i] <- "Elso Goñi, Wilson"}
                if(x[i] == "Enciso, Carlos"){o[i] <- "Enciso Christiansen, Carlos"}
                if(x[i] == "Escardo"){o[i] <- "Escardo Monge, Alejandro Alberto"}
                if(x[i] == "Etcheverrry, Juan"){o[i] <- "Etcheverry, Juan"}
                if(x[i] == "Fa Robaina"){o[i] <- "Fa Robaina, Juan Carlos"}
                if(x[i] == "Favretti"){o[i] <- "Favretti Calvete, Aldo Ramon"}
                if(x[i] == "Fernandez Chaves, Alejo"){o[i] <- "Fernandez Chavez, Alejo"}
                if(x[i] == "Ferrari Sancez, Hugo"){o[i] <- "Ferrari Sanchez, Hugo"}
                if(x[i] == "Ferreira Chavez"){o[i] <- "Ferreira Chaves, Ruben"}
                if(x[i] == "Ferrizo Ferreira, Luis A."){o[i] <- "Ferrizo Ferreira, Luis Alberto"}
                if(x[i] == "Ferrizzo , Luis Alberto"){o[i] <- "Ferrizo Ferreira, Luis Alberto"}
                if(x[i] == "Francolino, Rubens W."){o[i] <- "Francolino, Rubens Walter"}
                if(x[i] == "Fresia, Carlos M."){o[i] <- "Fresia, Carlos Maria"}
                if(x[i] == "Gaione"){o[i] <- "Gaione Scaglia, Heriberto Ariel"}
                if(x[i] == "Galo"){o[i] <- "Galo, Jose Maria"}
                if(x[i] == "Gandini"){o[i] <- "Gandini, Jorge"}
                if(x[i] == "Garat, Carlos"){o[i] <- "Garat, Carlos M."}
                if(x[i] == "Gargano"){o[i] <- "Gargano, Reinaldo"}
                if(x[i] == "Gil, Orlando"){o[i] <- "Gil Solares, Orlando"}
                if(x[i] == "Gomez, Larriera"){o[i] <- "Gomez Larriera, Francisco"}
                if(x[i] == "Gonzalez Perla, Humberto"){o[i] <- "Gonzalez Perla, Humberto A."}
                if(x[i] == "Goñi Castelao, Hector H."){o[i] <- "Goñi Castelao, Hector Homero"}
                if(x[i] == "Guadalupe Chocho, Ramon"){o[i] <- "Guadalupe Chocho, Ramon Isaac"}
                if(x[i] == "Guerra, Antonio"){o[i] <- "Guerra Caraballo, Antonio"}
                if(x[i] == "Guerrero silva"){o[i] <- "Guerrero Silva, Arturo"}
                if(x[i] == "Guntin"){o[i] <- "Guntin, Jose Luis"}
                if(x[i] == "Hackenbruch, Tabare"){o[i] <- "Hackenbruch Legnani, Tabare"}
                if(x[i] == "Hakenbruch Legnani, Tabare"){o[i] <- "Hackenbruch Legnani, Tabare"}
                if(x[i] == "Heber Füllgraff, Arturo"){o[i] <- "Heber Fulgraff, Arturo"}
                if(x[i] == "Heber, Luis A."){o[i] <- "Heber, Luis Alberto"}
                if(x[i] == "Hernandez garcia"){o[i] <- "Hernandez Garcia"}
                if(x[i] == "Hierro Lopez"){o[i] <- "Hierro Lopez, Luis A."}
                if(x[i] == "Hualde"){o[i] <- "Hualde Silva, Jose"}
                if(x[i] == "Hunter"){o[i] <- "Hunter, Jorge"}
                if(x[i] == "Ibarra, Doreen"){o[i] <- "Ibarra, Doreen Javier"}
                if(x[i] == "Jaurena, Eduardo"){o[i] <- "Jaurena, Eduardo Humberto"}
                if(x[i] == "Lacalle, Luis Alberto"){o[i] <- "Lacalle Pou, Luis Alberto"}
                if(x[i] == "Laguarda"){o[i] <- "Laguarda, Manuel"}
                if(x[i] == "Lenzi, Oscar"){o[i] <- "Lenzi Lateulade, Oscar"}
                if(x[i] == "loblowitz"){o[i] <- "Loblowitz Ernst, Stefan"}
                if(x[i] == "Loblowitz"){o[i] <- "Loblowitz Ernst, Stefan"}
                if(x[i] == "Lombardo, Ricardo"){o[i] <- "Lombardo, Ricardo J."}
                if(x[i] == "Long"){o[i] <- "Long Garat, Ruperto Enzo"}
                if(x[i] == "Lopez Balestra, Oscar"){o[i] <- "Lopez Balestra, Oscar C."}
                if(x[i] == "Lopez martinez"){o[i] <- "Lopez Martinez, Nestor"}
                if(x[i] == "Maestre Diaz"){o[i] <- "Maestre Diaz, Washington Leonel" }
                if(x[i] == "Mahia, Jose"){o[i] <- "Mahia, Jose Carlos"}
                if(x[i] == "Mahia, Jose C."){o[i] <- "Mahia, Jose Carlos"}
                if(x[i] == "Manzi"){o[i] <- "Manzi Santos, Miguel Angel"}
                if(x[i] == "Matos Pugliese, Julio C."){o[i] <- "Mattos Pugliese, Julio C."}
                if(x[i] == "Mattos Pugliese, Julio"){o[i] <- "Mattos Pugliese, Julio C."}
                if(x[i] == "Mc Alister"){o[i] <- "Mc Alister Green, Ernesto Juan"}
                if(x[i] == "Mederos, Carminillo"){o[i] <- "Mederos Da Costa, Carminillo"}
                if(x[i] == "Melo Santamarina, Eden"){o[i] <- "Melo Santa Marina, Eden"}
                if(x[i] == "Millor, Pablo"){o[i] <- "Millor Coccaro, Pablo"}
                if(x[i] == "Muniz Durand"){o[i] <- "Muniz Durand, Horacio" }
                if(x[i] == "Muñoz"){o[i] <- "Muñoz Cosentino, Clemente"}
                if(x[i] == "Nan Sauleda"){o[i] <- "Nan Sauleda, Luis Mario"}
                if(x[i] == "Nion"){o[i] <- "Nion, Antonio"}
                if(x[i] == "Olazabal"){o[i] <- "Olazabal, Walter"}
                if(x[i] == "Ovalle"){o[i] <- "Ovalle, Jose Luis"}
                if(x[i] == "Oxacelhay, Juan"){o[i] <- "Oxacelhay, Juan Antonio"}
                if(x[i] == "Pacheco Klein, Jorge"){o[i] <- "Pacheco Klein, Jorge Manuel"}
                if(x[i] == "Paysse, Daniela"){o[i] <- "Paysee, Daniela"}
                if(x[i] == "Peña, Adriana"){o[i] <- "Peña Hernandez, Adriana"}
                if(x[i] == "Perdomo, Alberto"){o[i] <- "Perdomo Gamarra, Alberto"}
                if(x[i] == "Pereira, Ramon"){o[i] <- "Pereira Paben, Ramon"}
                if(x[i] == "Perez Alvarez"){o[i] <- "Perez Alvarez, Manuel"}
                if(x[i] == "Pica, Humberto"){o[i] <- "Pica Ferrari, Humberto" }
                if(x[i] == "Ponce de Leon, Martin"){o[i] <- "Ponce De Leon, Martin"}
                if(x[i] == "Porras, Elias Alberto"){o[i] <- "Porras Larralde, Elias Alberto" }
                if(x[i] == "Pou, Ma Julia"){o[i] <- "Pou, Maria Julia"}
                if(x[i] == "Pozzolo, Luis B."){o[i] <- "Pozzolo, Luis Bernardo"}
                if(x[i] == "Requiterena, Alfonso"){o[i] <- "Requiterena Vogt, Alfonso"}
                if(x[i] == "Riesgo, Walter"){o[i] <- "Riesgo Larraz, Walter"}
                if(x[i] == "Rocha Imaz, Ricardo"){o[i] <- "Rocha Imaz, Ricardo Juan"}
                if(x[i] == "Rodino, Eduardo"){o[i] <- "Rodino Varela, Eduardo"}
                if(x[i] == "Rodriguez Apelo"){o[i] <- "Rodriguez Apelo, Raul"}
                if(x[i] == "Rodriguez Camusso, A. Francisco"){o[i] <- "Rodriguez Camusso, Francisco"}
                if(x[i] == "Rondan, Juan C"){o[i] <- "Rondan, Juan Carlos"}
                if(x[i] == "Rossi,  Victor"){o[i] <- "Rossi, Victor"}
                if(x[i] == "Ruiz Morena"){o[i] <- "Ruiz Morena, Ruben Alberico"}
                if(x[i] == "Sanchez Cal, Dardo"){o[i] <- "Sanchez Cal, Dardo Angel"}
                if(x[i] == "Sande, Pedro"){o[i] <- "Sande, Adolfo Pedro"}
                if(x[i] == "Sanguinetti, Julio M."){o[i] <- "Sanguinetti, Julio Maria"}
                if(x[i] == "Sanguinetti, Jullio M."){o[i] <- "Sanguinetti, Julio M."}
                if(x[i] == "Saravia, Diana"){o[i] <- "Saravia Olmos, Diana"}
                if(x[i] == "Sedarri, Edison"){o[i] <- "Sedarri Luaces, Edison"}
                if(x[i] == "Senatore, Luis"){o[i] <- "Senatore, Luis A."}
                if(x[i] == "Siazaro Andreotti, Juan C,"){o[i] <- "Siazaro Andreotti, Juan C."}
                if(x[i] == "Silveira Zabala, Jorge"){o[i] <- "Silveira Zavala, Jorge Eladio"}
                if(x[i] == "Silveira Zavala"){o[i] <- "Silveira Zavala, Jorge Eladio"}
                if(x[i] == "Singer, Juan A." ){o[i] <- "Singer, Juan Adolfo" }
                if(x[i] == "Singlet"){o[i] <- "Singlet, Manuel Maria"}
                if(x[i] == "Singlet, Manuel"){o[i] <- "Singlet, Manuel Maria"}
                if(x[i] == "Soares de Lima"){o[i] <- "Soares De Lima, Carlos"}
                if(x[i] == "Soto Bermudez, Carlos N."){o[i] <- "Soto Bermudez, Carlos Norberto"}
                if(x[i] == "Stirling, Guilllermo"){o[i] <- "Stirling, Guillermo"}
                if(x[i] == "Storace, Nicolas"){o[i] <- "Storace Montes, Nicolas"}
                if(x[i] == "Suarez Lerena, Carlos"){o[i] <- "Suarez Lerena, Carlos Enrique"}
                if(x[i] == "Toledo, Hermes"){o[i] <- "Toledo Antunez, Hermes"}
                if(x[i] == "Tovagliari"){o[i] <- "Traversoni, Alfredo"}
                if(x[i] == "Varela, Carlos"){o[i] <- "Varela Nestier, Carlos"}
                if(x[i] == "Vega, Alvaro"){o[i] <- "Vega Llanes, Alvaro"}
                if(x[i] == "Vinci"){o[i] <- "Vinci Lopez, Leonardo Jose"}
                if(x[i] == "Zaffaroni Ortiz, Alfredo"){o[i] <- "Zaffaroni Ortiz, Alfredo Cesar"}
                if(x[i] == "Zanoniani"){o[i] <- "Zanoniani, Rodolfo"}
      }
return(o)
}
```

</details>

Para completar el texto y obtener la estructura de datos necesaria
debemos ejecutar esta funciona y volver a compilar por legislador:

``` r
text$legislator <- parse_names(text$legislator)
text <- speech::speech_recompiler(text)
glimpse(text)
```

    #> Rows: 3,719
    #> Columns: 8
    #> $ legislator  <chr> "Aguirre Ramirez, Gonzalo", "Aguirre Corte, Numa", "Amaro …
    #> $ legislature <int> 42, 42, 42, 42, 42, 42, 42, 42, 42, 42, 42, 42, 42, 42, 42…
    #> $ chamber     <chr> "ASAMBLEA GENERAL", "ASAMBLEA GENERAL", "ASAMBLEA GENERAL"…
    #> $ date        <chr> "1986-01-07", "1986-01-07", "1986-01-07", "1986-01-07", "1…
    #> $ id          <chr> "Diario_0025.txt", "Diario_0025.txt", "Diario_0025.txt", "…
    #> $ speech      <chr> "SEÑOR AGUIRRE    Pido la palabra  SEÑOR AGUIRRE    Lo que…
    #> $ party       <chr> "PN", "PN", "PC", "PC", "PN", "FA", "FA", "PC", "PC", "PN"…
    #> $ doc_type    <chr> "html", "html", "html", "html", "html", "html", "html", "h…

#### Extracción de votaciones nominales

El código para detectar votaciones nominales elabora un conjunto de
expresiones regulares que permiten detectar la presencia de votaciones
nominales y construye una estructura de datos, de nuevo, en la que cada
fila es un legislador con su correspondiente voto (Afirmativo o
Negativo), a modo de ejemplo:

``` r
urls <- speech_url(chamber = "D", days = c("2002-06-12", "2004-04-14"))
rollcall <- speech_rollcall(file =  urls)


rollcall
#> # A tibble: 165 × 10
#>    legislator       vote argument speech chamber date       legislature rollcall
#>    <chr>           <dbl>    <dbl> <chr>  <chr>   <date>           <int>    <int>
#>  1 ABDALA              0        0 SEÑOR… CAMARA… 2002-06-12          45        1
#>  2 AGAZZI              1        1 SEÑOR… CAMARA… 2002-06-12          45        1
#>  3 AMEN VAGHETTI       0        0 SEÑOR… CAMARA… 2002-06-12          45        1
#>  4 AMORIN BATLLE       0        0 SEÑOR… CAMARA… 2002-06-12          45        1
#>  5 ARAUJO              0        1 SEÑOR… CAMARA… 2002-06-12          45        1
#>  6 ARGENZIO            0        1 SEÑOR… CAMARA… 2002-06-12          45        1
#>  7 ARGIMON             0        0 SEÑOR… CAMARA… 2002-06-12          45        1
#>  8 ARRARTE FERNAN…     1        0 SEÑOR… CAMARA… 2002-06-12          45        1
#>  9 ARREGUI             1        1 SEÑOR… CAMARA… 2002-06-12          45        1
#> 10 BARAIBAR            1        0 SEÑOR… CAMARA… 2002-06-12          45        1
#> # ℹ 155 more rows
#> # ℹ 2 more variables: id <chr>, sex <dbl>

summary(rollcall)
#> # A tibble: 2 × 10
#>   Chamber Date       Legislators Affirmative Negative prop_AF prop_NG prop_women
#>   <chr>   <date>           <int>       <dbl>    <dbl>   <dbl>   <dbl>      <dbl>
#> 1 CRR     2002-06-12          92          45       47    48.9    51.1       13.0
#> 2 CRR     2004-04-14          73          32       41    43.8    56.2       15.1
#> # ℹ 2 more variables: prop_arg <dbl>, rc <int>
```

La virtud que tiene esta función es que permite detectar las votaciones
nominales a las que hacíamos referencia en la nota 1 que corresponden a
votaciones de cargos de representación de la gestión de la cámara. Estas
votaciones tienen una estructura particular que hace que detectarlas sea
relativamente sencillo y por esa razón no son recuperadas. También se
puede observar que la función devuelve una variable en donde se detecta
si hay un discurso asociado a la votación o no (variable `argument`).

### Simulación de votaciones nominales

Los modelos de escalamiento multidimensional necesarios para el objetivo
planteado son muy demandantes en datos para poder elaborar un espacio
euclidiano. Esto representa un problema ya que las votaciones nominales
asociadas a los vetos no son suficientes en todas las legislaturas como
para obtener un escalamiento confiable. A este problema la literatura no
ofrece soluciones evidentes, pero se han implementado distintas
alternativas (como duplicar votaciones), a los efectos de solucionar el
problema de la cantidad para iniciar el escalamiento. En el caso
particular hemos avanzado en una estrategia bayesiana que permite
generar votaciones nominales en donde el prior es la votación anterior
(va aprendiendo de la elección anterior). Esto no es más que un proceso
markoviano (MCMC, Markov Chain Monte Carlo) con un Gibbs Sampling. Es
decir, el algoritmo implementado converge hacia la distribución conjunta
deseada a medida que se realizan más iteraciones. La idea clave es que,
al muestrear de las distribuciones condicionales, el algoritmo explora
el espacio de las variables de manera efectiva, permitiendo que la
muestra converja a la distribución conjunta subyacente.

De este modo, se implementó la lógica de este proceso para generar
votaciones nominales para poder iniciar el escalamiento con votaciones
que mantienen la misma distribución. En el siguiente apartado se
presenta el código de este proceso en donde se elaboraron cuatro
funciones que determinan la generación de las votaciones. La función
principal es `aux1` que es la que elabora votaciones condicionado a la
anterior y resuelve con la moda (`Mode`) como criterio de decisión.

``` r
### [1]
Mode <- function(x) {
        ux <- unique(x)
        ux[which.max(tabulate(match(x, ux)))]
}
### [2]
aux1 <- function(x, vot, n){
        wid <- ncol(x)
        len <- nrow(x)
        x[,wid+1] <- x[,vot]
        x[1,wid+1] <- ifelse(x[1,vot] != 0, Mode(x[which(x$LEMA == x$LEMA[1]), vot]), 0)
        for(j in 2:n){
                x[,wid+j] <- x[,wid+j-1]
                x[(j-len*(j%/%len))+1,wid+j] <- 
                     ifelse(x[(j-len*(j%/%len))+1,vot] != 0, 
                            Mode(x[which(x$LEMA == x$LEMA[(j-len*(j%/%len))+1]), vot]), 0)
        }
        return(x[,(wid+1):(wid+n)])
}
### [3]
aux2 <- function(x,vots,n){
        out <- list()
        for(i in 1:length(vots)){out[[i]] <- aux1(x, vots[i], n)}
        return(out)
}
```

De este modo, implementamos la simulación para todas las legislaturas:

``` r
legislaturas <- as.character(c(25, 27, 29, 30,31, 34:46)) 
multiplo <- 4L 
dim_matrix <- numeric()
system.time(
for(i in 1:length(legislaturas)){
        
        legis  <- legislaturas[i]
        dataL  <- base2[base2$legislatura==legis, c(1:7,grep(paste("^a", legis, sep=""), names(base2)))]
        dataL2 <- cbind("NOMBRE"  = dataL[,3],
                        "LEMA"    = dataL[,6],
                        "SUBLEMA" = dataL[,7],
                        "LISTA"   = "aji",
                 dataL[,8:ncol(dataL)])

        setwd(directorio_GENERADOR)
        source("GENERADOR.R")
                x    <- dataL2
                vots <- c(5:ncol(dataL2))
                n    <- multiplo*nrow(dataL2)
         prueba <- aux2(x, vots, n)
         
         setwd(directorio_destino_simulaciones)
         data_export <- cbind.data.frame(dataL2, as.data.frame(prueba))
         write.csv(data_export, paste("L", legis, ".csv", sep=""))
         dim_matrix[i] <- ncol(data_export)*nrow(data_export)
}
)
```

En este punto, llegamos a la extracción del texto y de las votaciones
nominales necesarias para iniciar el proceso de modelado de los dato.

## Modelos de escalamiento

### Votos

El objetivo del modelado a partir de votaciones nominales es la
construcción de un punto ideal para cada legislador. El punto ideal de
un legislador refiere a su ubicación en un espacio de preferencias, dado
el conjunto de preferencias de los demás legisladores. El marco espacial
más simple para ordenar las preferencias de los legisladores, y en
consecuencia de los partidos políticos, se compone de una única
dimensión en donde los legisladores pueden ser ordenados en un continuo,
por ejemplo, Izquierda-Derecha. En un espacio unidimensional, los puntos
ideales se ubican en una línea imaginaria representando el continuo. En
el caso de dos dimensiones, los puntos ideales se ubican en un plano en
donde cada legislador representa la intersección de cada una de las
dimensiones.

El ordenamiento de las preferencias que culminan en el punto ideal de un
legislador es afectado por dos factores. En primer lugar, por la
cantidad de dimensiones que están en juego en la reconstrucción del
espacio político, y en segundo lugar por los legisladores de referencia
(también llamado *polarity*) que se tiene para cada dimensión. Uno de
los principales objetivos de la estimación de puntos ideales es obtener
una salida gráfica que permita observar la distribución de estos en un
plano de una o más dimensiones.

El escalamiento a partir de votaciones nominales es bastante flexible
para ser un modelo supervisado. La única restricción que presenta es que
se debe establecer quién es el legislador que va a actuar como ‘polar’.
Esto es, el legislador que por sus características puede ser considerado
que está en uno de los extremos del espectro por el criterio que el
investigador considere. Una dificultad que aparece en estos casos es que
el criterio (por ejemplo, que el legislador seleccionado sea el más de
izquierda) no siempre esté presente en las votaciones o tenga muy pocas
votaciones por lo que la referencia de ese legislador se vuelve sesgada.
En estos casos es posible establecer como polar al que se pueda
considerar más de derecha y en esos casos la escala resultante se puede
invertir.

El código que sigue es la función que se hizo para correr el modelo del
paquete `wnominate` ajustado a los datos que tenemos.

``` r
run_wn <- function(data, polar = character(), legislature = numeric()){

        base <- data
        polar_1 <- which(base$NOMBRE == polar)[1]
        legis_vector <- character()
        for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep = "")}
        names <- paste(legis_vector, as.character(base[,1]))
        base <- as.matrix(base)
        label_party <- matrix(base[, 2], nrow = length(base[, 2]), ncol = 1)
        colnames(label_party) <- "party"
        base <- base[, -c(1,2,3,4)]
        rc <- rollcall(base,
                       yea = 1,
                       nay = 4,
                       notInLegis = 0,
                       legis.names = names,
                       legis.data = label_party,
                       desc = "base Votes")
        result <- wnominate(rc, dim = 91, polarity = polar_1)
        coord <- result$legislator[,c("party", "coord1D")]
        coord$coord1D <- coord$coord1D * -1
        name  <- substring(rownames(coord), 15, 100)
        coord <- cbind(legislator = name, coord[,1:2])
        rownames(coord) <- NULL
        coord$legislature <- legislature
        coord$id_wn <- paste(coord$legislator, coord$party, coord$legislature)
        na.omit(coord)

}
```

Una vez que se tiene los votos, los polares, y la función adaptada a los
datos solo resta ejecutar la función para cada legislatura e ir
guardando las coordenadas resultantes para poder visualizar y analizar
luego los resultados. En el código que se optó por iterar para cada
legislatura paso a paso para que quede registro de las decisiones que se
tomaron en cada legislatura con respecto a los polares que son los que
inician el escalamiento.

<details>
<summary>
<b><a style="cursor: pointer;">Ver código </a></b>
</summary>

``` r
library("wnominate")
library("xlsx")

#============================#
#        Leg 25              # 
#============================#
rm(list=ls())
base <- read.csv("L25.csv")
polar_1 <- which(base$NOMBRE=="areco") 
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)


#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L25", row.names = F)


#============================#
#        Leg 27              # 
#============================#

rm(list=ls())
#base <- read.csv(file.choose())
base <- read.csv("L27.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="otero") 
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L27", row.names=F, append = T)     



#============================#
#        Leg 29              # 
#============================#

base <- read.csv("L29.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="casaravilla") 
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L29", row.names=F, append = T)     


#============================#
#        Leg 30              # 
#============================#

base <- read.csv("L30.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="manini_rios") 
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L30", row.names=F, append = T)     



#============================#
#        Leg 31              # 
#============================#
base <- read.csv("L31.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="espalter") 
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L31", row.names=F, append = T)  



#============================#
#        Leg 34              # 
#============================#
base <- read.csv("L34.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="mora_otero")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L34", row.names=F, append = T)  



#============================#
#        Leg 35              # 
#============================#
base <- read.csv("L35.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="brum")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L35", row.names=F, append = T)  


#============================#
#        Leg 36              # 
#============================#
base <- read.csv("L36.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="manini_rios")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L36", row.names=F, append = T)  


#============================#
#        Leg 37              # 
#============================#
base <- read.csv("L37.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="barbieri")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L37", row.names=F, append = T)  


#============================#
#        Leg 38              # 
#============================#

base <- read.csv("L38.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="silva")[2]
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L38", row.names=F, append = T)  


#============================#
#        Leg 39              # 
#============================#

base <- read.csv("L39.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="fischer")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L39", row.names=F, append = T)  




#============================#
#        Leg 40              # 
#============================#
base <- read.csv("L40.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="caputi")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L40", row.names=F, append = T)  

#============================#
#        Leg 41              # 
#============================#

base <- read.csv("L41.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="sapelli")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L41", row.names=F, append = T)  



#============================#
#        Leg 42              # 
#============================#
base <- read.csv("L42.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="tarigo")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L42", row.names=F, append = T)  

#============================#
#        Leg 43              # 
#============================#

base <- read.csv("L43.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="aguirre")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L43", row.names=F, append = T)  


#============================#
#        Leg 44              # 
#============================#
base <- read.csv("L44.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="batalla")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L44", row.names=F, append = T)  


#============================#
#        Leg 45              # 
#============================#
base <- read.csv("L45.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="hierro_lópez")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L45", row.names=F, append = T)  


#============================#
#        Leg 46              # 
#============================#
base <- read.csv("L46.csv"); dim(base)
polar_1 <- which(base$NOMBRE=="nin")
legis_vector <- character()
for(i in 1:nrow(base)){legis_vector[i] <- paste("Legislador", 99+i, sep="")}
names <- paste(legis_vector, as.character(base[,2]))
base <- as.matrix(base)
label_party <- matrix(base[, 3], length(base[, 3]), 1)
colnames(label_party) <- "party"
base <- base[, -c(1,2,3,4,5)]
rc <- rollcall(base, yea = 1, nay = 4, notInLegis = 0, 
               legis.names = names,legis.data = label_party, desc = "base Votes")
result <- wnominate(rc, dim=1, polarity = polar_1)
summary(result, verbose=TRUE)
plot.coords (result, plotBy="party", color=TRUE, legend.x=1.5)

#--------------------
# GUARDAR CORDENADAS
#--------------------
coord <- na.omit(result$legislator[,c("party", "coord1D")])
nombres <- substring(rownames(coord), 15,60)
coord <- cbind(legislador = nombres, coord[,1:2])
rownames(coord) <- NULL
write.xlsx(coord, file = "all_coord_G_O.xlsx", sheetName = "coord_L46", row.names=F, append = T)  
```

</details>

## Texto

En el caso del uso de texto como fuente da datos el proceso es similar
al escalamiento con votos. Dado que el proceso computacional es mucho
más costoso vamos a concentrarnos en las legislaturas desde 1985 hasta
2010.<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup> Los
modelos que vamos a utilizar son dos: uno supervisado y otro no
supervisado. El modelo supervisado utiliza polares pero que son
referencia para iniciar el escalamiento. Lo que se usa de referencia en
estos modelos es el texto que fue dicho por ese legislador de referencia
(análogo al *polar* en el escalamiento con votaciones nominales). En el
caso de los modelos no supervisados el escalamiento es más perezoso ya
que no usa referencia, sino que selecciona uno al azar y se van
ajustando a medida que van ingresados datos al modelo.

El código que le sigue son las funciones que se desarrollaron para
ejecutar los dos modelos de escalamiento con texto:

1.  Wordscores (Supervisado)
2.  Wordfish (No supervisado)

``` r
ws_uy <- function(data, legislature){

        base <- data
        legislatura <- legislature
        ## SE PREPARA BASE -----------------------------------------------------
        speech2 <-
                base %>%
                speech_recompiler(compiler_by = c("legislator", "party", "legislature", "chamber"))

        speech2$palabras  = speech_word_count(speech2$speech)
        speech2$ref_score = NA
        speech2$mediana   = median(speech2$palabras)
        speech2 <- speech2[which(speech2$legislature == legislatura), ]

        if(legislatura == 42){
                ## REF LEGISLATURE 42 ==========================================
                speech2[speech2$legislator == "Araujo, Jose German",           "ref_score" ] <- -1
                speech2[speech2$legislator == "Millor Coccaro, Pablo",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Fau, Yamandu",                  "ref_score" ] <- 0
                speech2[speech2$legislator == "Heber, Luis Alberto",           "ref_score" ] <- 0.7
                speech2[speech2$legislator == "Lacalle Herrera, Luis Alberto", "ref_score" ] <- 0.8
                speech2[speech2$legislator == "Gargano, Reinaldo",             "ref_score" ] <- -0.8
        }
        if(legislatura == 43){
                ## REF LEGISLATURE 43 ==========================================
                speech2[speech2$legislator == "Millor Coccaro, Pablo",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Gargano, Reinaldo",             "ref_score" ] <- -0.8
                speech2[speech2$legislator == "Garcia Pintos, Daniel",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Heber, Luis Alberto",           "ref_score" ] <- 0.8
                speech2[speech2$legislator == "Chifflet, Guillermo",           "ref_score" ] <- -1
                speech2[speech2$legislator == "Fau, Yamandu, Pablo",           "ref_score" ] <- 0
        }
        if(legislatura == 44){
                ## REF LEGISLATURE 44 ==========================================
                speech2[speech2$legislator == "Millor Coccaro, Pablo",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Mujica, Jose",                  "ref_score" ] <- -1
                speech2[speech2$legislator == "Fau, Yamandu, Pablo",           "ref_score" ] <- 0
                speech2[speech2$legislator == "Chifflet, Guillermo",           "ref_score" ] <- -1
                speech2[speech2$legislator == "Garcia Pintos, Daniel",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Heber, Luis Alberto",           "ref_score" ] <- 0.8
        }
        if(legislatura == 45){
                ## REF LEGISLATURE 45 ==========================================
                speech2[speech2$legislator == "Millor Coccaro, Pablo",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Garcia Pintos, Daniel",         "ref_score" ] <- 1
                speech2[speech2$legislator == "Mujica, Jose",                  "ref_score" ] <- -1
                speech2[speech2$legislator == "Fau, Yamandu, Pablo",           "ref_score" ] <- 0
                speech2[speech2$legislator == "Chifflet, Guillermo",           "ref_score" ] <- -1
                speech2[speech2$legislator == "Heber, Luis Alberto",           "ref_score" ] <- 0.8
        }
        if(legislatura == 46){
                ## REF LEGISLATURE 46 ==========================================
                speech2[speech2$legislator == "Heber, Luis Alberto",           "ref_score" ] <- 0.8
                speech2[speech2$legislator == "Couriel, Alberto",              "ref_score" ] <- -0.8
                speech2[speech2$legislator == "Rosadilla, Luis",               "ref_score" ] <- -0.8
                speech2[speech2$legislator == "Sanguinetti, Julio Maria",      "ref_score" ] <- 0.8
                speech2[speech2$legislator == "Penades, Gustavo",              "ref_score" ] <- 0.8
                speech2[speech2$legislator == "Topolansky, Lucia",             "ref_score" ] <- -1
        }

        ## CORPUS --------------------------------------------------------------
        corpus_speech <-  corpus(speech2$speech)
        docvars(corpus_speech, "ref_score")   <- speech2$ref_score
        docvars(corpus_speech, "Party")       <- speech2$party
        docvars(corpus_speech, "legislador")  <- speech2$legislator

        ## SCALING -------------------------------------------------------------
        dfm_42 <- dfm(corpus_speech,
                      stem = TRUE,
                      tolower = TRUE,
                      remove = c(stopwords("spanish"),"Uruguay","uru*","senador","ley","artículo","proyecto"),
                      remove_punct = TRUE,
                      remove_numbers = TRUE,
                      verbose = TRUE)

        ## MODEL ---------------------------------------------------------------
        modelo_42<- textmodel_wordscores(dfm_42, y = docvars(corpus_speech, "ref_score"), smooth = 1)
        pred_ws_42 <- predict(modelo_42, se.fit = TRUE, newdata = dfm_42)
        names(pred_ws_42$fit) <- speech2$legislator
        #textplot_scale1d(pred_ws_42, groups = speech2$party)

        out <- data.frame(legislador  = names(pred_ws_42$fit),
                          score       = unname(pred_ws_42$fit),
                          error       = pred_ws_42$se.fit,
                          legislatura = legislatura,
                          partido     = speech2$party,
                          metodo      = "ws",
                          stringsAsFactors = FALSE)
        out
}


wfish <- function(data, legislature){

     base <- data
     legislatura <- legislature

     ## SE PREPARA BASE -------------------------------------------------------------
     speech2 <-
          base %>%
          filter(chamber == "ASAMBLEA GENERAL") %>%
          speech_recompiler(compiler_by = c("legislator", "party", "legislature", "chamber"))

     speech2$palabras  = speech_word_count(speech2$speech)
     speech2$ref_score = NA
     speech2$mediana   = median(speech2$palabras)
     speech2 <- speech2[which(speech2$legislature == legislatura), ]

     ## SE PREPARA CORPUS -----------------------------------------------------------
     corpus_speech <-  corpus(speech2$speech)
     docvars(corpus_speech, "Party")       <- speech2$party
     docvars(corpus_speech, "legislador")  <- speech2$legislator

     ## SE INICIA ESCALAMIENTO ------------------------------------------------------
     dfm_tot <- dfm(corpus_speech,
            stem = TRUE,
            tolower = TRUE,
            remove = c(stopwords("spanish"),"Uruguay","uru*","senador","ley","artículo","proyecto"),
            remove_punct = TRUE,
            remove_numbers = TRUE,
            verbose = TRUE)

     fish <- textmodel_wordfish(dfm_tot, sparse=TRUE)
     fish$docs <- speech2$legislator
     datos_ll <- data.frame(legislador  = fish$docs,
                            score       = fish$theta,
                            error       = fish$se.theta,
                            partido     = speech2$party,
                            legislature = legislature)
     datos_ll
}
```

## Análisis y visualización

El primer objetivo del escalamiento es visualizar las coordenadas. En
este punto fue fundamental la construcción de la base de datos y del
paquete de R de políticos uruguayos que hemos desarrollado para poder
contar con las etiquetaras partidarias de los
legisladores.<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup>
Es importante resaltar que en los diarios de sesión, la fuente de
información de donde extraemos los datos la referencia a legisladores
está despojada de la etiqueta partidaria. Y dado que nuestro interés
radica en los partidos como agentes es fundamental contar con esta
información.

``` r
library(ggplot2)
library(ggjoy)
library(gridExtra)
library(dplyr)
library(xlsx)
library(ggridges)

datos <- here::here("data-raw", "all_coord_G_O.xlsx")
legis <- readxl::excel_sheets(datos)
legis <- legis[-length(legis)]
legis2 <- c(
        "L25: 1914-1917",
        "L27: 1920-1923",
        "L29: 1926-1929",
        "L30: 1929-1932",
        "L31: 1932-1933",
        "L34: 1943-1947",
        "L35: 1947-1951",
        "L36: 1951-1955",
        "L37: 1955-1959",
        "L38: 1959-1963",
        "L39: 1963-1967",
        "L40: 1967-1971",
        "L41: 1971-1973",
        "L42: 1985-1989",
        "L43: 1990-1994",
        "L44: 1995-1999",
        "L45: 2000-2004"
)
lista <- list()
for(i in seq_along(legis)){
        lista[[i]] <- readxl::read_excel(datos, sheet=legis[i])
        lista[[i]]$legislatura <- legis2[i]
}


lfin <- do.call(rbind, lista) 
lfin2 <- filter(lfin, party %in% c("PN","PC","FA"))
lfin2$colema <- ifelse(lfin2$party=="PN", "blue", ifelse(lfin2$party=="PC", "red", "green"))
ggplot(lfin2) +
        geom_joy( aes(x = coord1D, y = legislatura, fill=colema),alpha =0.5) + 
        theme_joy(grid=FALSE) + 
        theme (plot.title = element_text(size=rel(0.6)), plot.subtitle = element_text(size=rel(0.6)) ) +
        theme (axis.text = element_text(size=rel(0.6))) +
        labs(title = "", x = "", y = "", 
             subtitle = "") + 
        scale_fill_discrete(name = "", labels = c("Partido Colorado", "Frente Amplio", "Partido Nacional")) +
        theme(legend.position = "bottom", 
              legend.direction = "horizontal",
              legend.key.size = unit(0.3, "cm"))
```

![](README_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

En el gráfico se observan varias coas que son bien interesantes:

1.  Hay variación en el tiempo entre legislaturas y entre partidos. Esto
    pone en tensión la hipótesis de estabilidad del sistema. Es decir, a
    lo largo de siglo los partidos han presentado variación los
    movimientos en el espacio ideológico. Lo que si puede verse con
    cierta estabilidad es la estructura competitiva y programática ya
    que entre partidos hay diferenciación.
2.  Dentro de los partidos y variación que pueden ser identificadas como
    grupos internos o sectores (fracciones). Esto es super interesante
    ya que no hay evidencia al respecto en la literatura. Hay pequeños
    desvíos que hacen referencia a las variaciones internas dentro de
    los partidos
3.  La distribución de los partidos muestra las distintas estrategias
    que han utilizado los partidos cuando se observa con la variable del
    partido que ganó o va a ganar en la elección siguiente.

### Texto vs. Voto

Para el caso del escalamiento de texto vemos que las coordenadas tienen
una forma un tanto diferente a la que encontramos en el caso de los
votos. En el gráfico que sigue se puede ver la distribución del especio
de coordenadas por partido a partir del texto:

``` r

fin <- rio::import("data-raw/scoresWordscores.xlsx")
graph_ws <-
        ggplot(fin) +
        xlim(-0.5, 0.5) +
        geom_joy( aes(x = score, y = partido, fill = partido), alpha = 0.4) +
        theme_joy(grid = TRUE ) +
        scale_y_discrete(breaks=c("FA", "PC", "PN"), expand = c(0.50, 0.05)) +
        scale_fill_grey(start = 0, end = .9, guide = FALSE) +
        facet_wrap(~substring(legislatura3, 13, nchar(legislatura3)), nrow = 1) +
        labs(title = "",
             x = "wordscores",
             y = "",
             caption = "PN: Partido Nacional; PC: Partido Colorado; FA: Frente Amplio"
             ) +
        theme_ipsum() +
        theme(panel.grid = element_blank(),
          panel.grid.major.x = element_blank(),
          panel.grid.minor.x = element_blank(),
          panel.grid.major.y = ggplot2::element_line(color = "#cbcbcb"),
          panel.grid.minor.y = element_blank(),
          #axis.title.y = element_text(size = 8, vjust = 3, angle = 0),
          #axis.text.y = element_text(size = rel(0.9)),
          axis.text.x=element_text(size=rel(0.9), angle=0),
          axis.line.x = element_line(),
          plot.title  = element_text(hjust = 0.5, face = "bold"),
          plot.subtitle = element_text(colour = "grey30", hjust = 0.5),
          plot.caption = element_text(colour = "grey50"),
          text = element_text(family = "Arial Narrow"),
          #legend.title = element_blank(),
          legend.position = "bottom",
          legend.justification = "left",
          legend.direction = "vertical"
          )

graph_ws
#> Picking joint bandwidth of 0.0372
#> Picking joint bandwidth of 0.0336
#> Picking joint bandwidth of 0.0252
#> Picking joint bandwidth of 0.0151
#> Warning: Removed 1 rows containing non-finite values (`stat_density_ridges()`).
#> Warning: The `guide` argument in `scale_*()` cannot be `FALSE`. This was deprecated in
#> ggplot2 3.3.4.
#> ℹ Please use "none" instead.
#> This warning is displayed once every 8 hours.
#> Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
#> generated.
```

![](README_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

En el gráfico se aprecian varias cosas interesantes:

1.  Hay poca variación entre partidos. Esto indica que la diferenciación
    no parece estar en el speech. Es decir, los partidos hablan entre
    ellos de forma similar.
2.  hay variación en el tiempo en el sistema. Hay pequeños corrimientos
    de las distribuciones, pero esto más que referirse a un cambio
    ideológico puede referir a un modo o una manera de dialogar por
    épocas.
3.  Ahora, dentro de los partidos se aprecian al igual que en el
    escalamiento con votos que aparece diferenciación dentro de los
    partidos: aparecen pequeños grupos dentro de los partidos.

El gráfico que le sigue es de 1985 a 2005 para votaciones nominales:

``` r
nom <- rio::import("data-raw/scoresNominates.xlsx")

graph_nom <-
        ggplot(nom) +
        xlim(-1.2, 1.2) +
        geom_joy( aes(x = coord1D, y = party, fill = party), alpha = 0.4) +
        theme_joy(grid = TRUE ) +
        scale_y_discrete(breaks=c("FA", "PC", "PN"), expand = c(0.50, 0.05)) +
        scale_fill_grey(start = 0, end = .9, guide = FALSE) +
        facet_wrap(~substring(legis3, 13, nchar(legis3)), nrow = 1) +
        labs(title = "",
             x = "wnominate",
             y = "",
             caption = "PN: Partido Nacional; PC: Partido Colorado; FA: Frente Amplio"
             ) +
        theme_ipsum() +
        theme(panel.grid = element_blank(),
          panel.grid.major.x = element_blank(),
          panel.grid.minor.x = element_blank(),
          panel.grid.major.y = ggplot2::element_line(color = "#cbcbcb"),
          panel.grid.minor.y = element_blank(),
          #axis.title.y = element_text(size = 8, vjust = 3, angle = 0),
          #axis.text.y = element_text(size = rel(0.9)),
          axis.text.x=element_text(size=rel(0.9), angle=0),
          axis.line.x = element_line(),
          plot.title  = element_text(hjust = 0.5, face = "bold"),
          plot.subtitle = element_text(colour = "grey30", hjust = 0.5),
          plot.caption = element_text(colour = "grey50"),
          text = element_text(family = "Arial Narrow"),
          #legend.title = element_blank(),
          legend.position = "bottom",
          legend.justification = "left",
          legend.direction = "vertical"
          )

graph_nom
```

![](README_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

## Discusión

En último lugar queremos cotejar a modo de cierre la relación entre el
voto y el discurso. Si bien ya veíamos que si bien ambos métodos ofrecen
resultados interesantes. A nivel descriptivo vemos que hay
diferenciación en el voto que no aparece en el discurso. Esto es bien
interesante ya que siguiere la idea de un sistema en donde hay
entendimiento en el dialogo y la comunicación en el tipo de lenguaje es
similar entre partidos, pero la diferenciación aparece a la hora de
votar.

En el coligo que le sigue cotejamos ambos métodos de escalamientos:

``` r
# ws
w <- fin['score']
names(w)[1] <- 'score_ws'
fin$legislador <- parse_names(fin$legislador)
w$id <- paste(fin$legislador, fin$legislatura, fin$partido, sep = "_")

# rc
n <- nom['coord1D']
names(n)[1] <- 'score_nom'
nom$legislator <- parse_names(nom$legislator)
n$id <- paste(nom$legislator, nom$legislature, nom$party, sep = "_")


# union
two <- full_join(w, n,  "id")
two <- two %>%
    separate(col = 2, into = c('name', 'legislature', 'party'), sep = "_") %>%
    filter(party %in% c('PN', 'PC', 'FA'))

header <- function(datos, separador = ": "){
        vec_name <- character()
        for(i in 1:length(datos)){
                if(datos[i] == 42) vec_name[i] <- paste("Legislature", "1985 - 1990", sep = separador)
                if(datos[i] == 43) vec_name[i] <- paste("Legislature", "1990 - 1995", sep = separador)
                if(datos[i] == 44) vec_name[i] <- paste("Legislature", "1995 - 2000", sep = separador)
                if(datos[i] == 45) vec_name[i] <- paste("Legislature", "2000 - 2005", sep = separador)
                if(datos[i] == 46) vec_name[i] <- paste("Legislature", "2005 - 2010", sep = separador)
        }
        vec_name
}



two$legislature <- header(two$legislature) #paste0("Legislature ", two$legislature, "°")
two$ptyname <- ifelse(two$party == "FA", "Frente Amplio",
                      ifelse(two$party == "PN", "Partido Nacional", "Partido Colorado"))


ggplot(two, aes(x = score_nom, y = score_ws, fill = ptyname)) +
    geom_point(shape = 21, size = 3, alpha = 0.8, color = "black") +
    scale_fill_grey(start = 0, end = .9) +
    xlim(-1, 1) +
    ylim(-0.5, 0.5) +
    labs(y = 'wordscores', x = 'wnominate', fill = '') +
    #geom_smooth(method = lm, se = TRUE) +
    facet_wrap(~substring(legislature, 13, nchar(legislature)), nrow = 1) +
    theme_ipsum() +
    theme(panel.grid = element_blank(),
          panel.grid.major.x = element_blank(),
          panel.grid.minor.x = element_blank(),
          panel.grid.major.y = ggplot2::element_line(color = "#cbcbcb"),
          panel.grid.minor.y = element_blank(),
          #axis.title.y = element_text(size = 8, vjust = 3, angle = 0),
          #axis.text.y = element_text(size = rel(0.9)),
          axis.text.x=element_text(size=rel(0.9), angle=0),
          axis.line.x = element_line(),
          plot.title  = element_text(hjust = 0.5, face = "bold"),
          plot.subtitle = element_text(colour = "grey30", hjust = 0.5),
          plot.caption = element_text(colour = "grey50"),
          text = element_text(family = "Arial Narrow"),
          #legend.title = element_blank(),
          legend.position = "bottom",
          legend.justification = "left",
          legend.direction = "vertical"
          )
```

![](README_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

En suma, es posible pensar que ambas estrategias ofrecen resultados que
tienen sentido en la tradición del sistema de partidos de Uruguay y en
la estructura de gobierno que los partidos han generado. Por un lado,
hay diferenciación dentro de los partidos y entre partidos, y esta
diferenciación no es estable en el tiempo. También vemos que la
moderación no es la norma. Estas dos ideas que han moldeado parte de la
interpretación tradicional del sistema al menos con esta evidencia se
ven tensionados. En el caso del texto, si vemos estabilidad y
moderación. Pero al mismo tiempo hay variación dentro de los partidos,
esto abona la idea de los sectores de los partidos que hablan a su
propio electorado intentando alejarse del mediando del partido sin
salirse de la distribución. Resta construir los datos de texto de 1985
para atrás para cotejar con las votaciones nominales.

## Referencias

Armstrong, D. A., Bakker, R., Carroll, R., Hare, C., Poole, K.
T.,Rosenthal, H. et al. (2014). Analyzing spatial models of choice and
judgmentwith R. CRC Press.

Black, D., Newing, R. A., McLean, I., McMillan, A. y Monroe, B.
L.(1958). The theory of committees and elections. Springer .

Br̈auninger, T., Debus, M. y Muller, J. (2013). Estimating policy
positions of political actors across countries and time. Working Paper .

Castillo, M. y Pérez, V. (2009). Esencia y espacio: análisis de los
programas del frente amplio y del partido nacional en las elecciones
nacionales de 2009.

Daniel Buquet y Niki Johnson Del Cambio a la Continuidad. Ciclo
electoral 2010, 105–131.

Clinton, J., Jackman, S. y Rivers, D. (2004). The statistical analysis
of rollcall data. American Political Science Review 98(2), 355–370.

Downs, A. (1957). An economic theory of political action in a democracy.
Journal of Political Economy 65(2), 135–150.

Gelman, A., Bafumi, J., Park, D. K. y Kaplan, N. (2005). Practical
issues in implementing and understanding bayesian ideal point
estimation. Political Analysis 13(13), 171–187. URL
<http://www.stat.columbia.edu/~gelman/research/published/171.pdf>.

Gelman, A., Carlin, J. B., Stern, H. S., Dunson, D. B., Vehtari, A.
yRubin, D. B. (2014). Bayesian data analysis, vol. 2. CRC press Boca
Raton,FL.

Guedes, A., Luján, D. y Kardjian, N. (2011). Presidentes, partidos e
ideología en Uruguay (1920-2009). DOL (Documento On-line)/FCS-CP;
01/11URL
<https://www.colibri.udelar.edu.uy/bitstream/123456789/4605/1/DOL%20CP%2001%2011.pdf>.

Hinich, M. J. y Munger, M. C. (1997). Analytical politics. Cambridge
University Press.

ICP, I. d. C. P. F. d. C. (). Encuesta de Elites. UdelaR.

Jackman, S. (2008). pscl: classes and methods for r developed in the
political science computational laboratory

Jackman, S. (2009). Bayesian analysis for the social sciences, vol. 846.
John Wiley & Sons.

Kruschke, J. (2014). Doing Bayesian data analysis: A tutorial with R,
JAGS, and Stan. Academic Press.

Laver, M. y Garry, J. (2000). Estimating policy positions from political
texts. American Journal of Political Science , 619–634.

PELA-USAL, M., Alcantara (). Encuesta Permanente de Elites. Universidad
de Salamanca.

Poole, K., Lewis, J., Lo, J. y Carroll, R. (2011). Scaling roll call
votes with wnominate in R. Journal of Statistical Software 42(14), 1–21.
URL http: //www.jstatsoft.org/v42/i14/.

Poole, K., Lewis, J., Lo, J. y Carroll, R. (2016a). oc: OC Roll Call
Analysis Software. URL <https://CRAN.R-project.org/package=oc>. R
package version 0.96.

Poole, K., Lewis, J., Rosenthal, H., Lo, J. y Carroll, R. (2016b).
Re-covering a basic space from issue scales in R. Journal of Statistical
Software 69(7), 1–21.

R Core Team (2017). R: A Language and Environment for Statistical
Computing. R Foundation for Statistical Computing, Vienna, Austria. URL
<https://www.R-project.org/>.

Volkens, A., Bara, J., Budge, I., McDonald, M. D. y Klingemann,

HD. (2013). Mapping policy preferences from texts: statistical solutions
for manifesto analysts, vol. 3. OUP Oxford.

## Notas

------------------------------------------------------------------------

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Hay un tercer tipo de
votación nominal que esta reglamentado por las normas de funcionamiento
de las cámaras que es cuando se votan cargos de responsabilidad de la
gestión de las cámaras (eg. presidente de cámara de representantes).

<sup><a id="fn.2" href="#fnr.2">2</a></sup> En este
[link](https://nicolas-schmidt.github.io/speech/index.html) se encuentra
el manual y el código fuente del paquete de R desarrollado. En este
[link](https://github.com/Nicolas-Schmidt/speech-ejemplos) se encuentra
un repositorio con ejemplos de uso y cómo solucionar problemas de
extracción de texto que es difícil recuperar por las condiciones del
documento.

<sup><a id="fn.3" href="#fnr.3">3</a></sup> Los diarios de sesión
previos a 1985 son escaneados. Esto hace que el proceso de recuperación
del texto sea mucho más costoso que recuperar votaciones.

<sup><a id="fn.4" href="#fnr.4">4</a></sup> Políticos uruguayos
[link](https://nicolas-schmidt.github.io/puy/)
