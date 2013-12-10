Copyright (c) [2013] [Ramon Franquesa Alberti, Carlos Martín Isla , Gonzalo Lopez Lillo , Aleix Gras Godoy ]

Proyecto Deteccion de Evento
El código proporcionado sirve para clasificar un conjunto de imágenes según el tipo de evento al que pertenecen.

A continuación un breve resumen de explicación del codigo

1.	Descriptor
	Entrada: 
La entrada consiste en una serie de directorios de nombre event_id con las fotos ordenadas respecto a verdad terreno, que a su vez han sido generadas por una función que lee la verdad terreno ( csv separado por tabs) y distribuye las imágenes en estos directorios.

	Funciones Utilizadas (breve descripción)
	model= LecturaImatges(dir, flag)
Extrae características de las imágenes de un directorio y las almacena en una matriz. Cada fila es un vector característica de una imagen de entrenamiento.
si flag=0, extrae histogramas luminancia, y si es 1, extrae HOG.
	vector=HistogramaBlocs(I)
Histograma por bloques  64 bloques y concatena los histogramas uno tras otro
	H=histograma(im)
Histograma luminancia de 256 bins. Funciona medianamente bien para concerts. Esa fue la idea original.
	H=HOG2(im)
HOG.16 bloques por imagen. 16 bins por bloque. Extrae texturas y formas en concreto la dirección de cada pixel (arctg(dx,dy)). Después, elabora un histograma de arcotangentes por cada bloque.
http://www.mathworks.com/matlabcentral/fileexchange/28689-hog-descriptor-for-matlab/content/HOG.m

	Descripcion del main
	1.	Se limpia y se crea una lista con los event_type
	2.	Se recuperan las imágenes de cada directorio /event_type del proyecto y se extrae la caracteristica en cada recuperación.
	3.	Se concatenan todas las caracteristicas y se exportan a un fichero bin:
-Primera fila: vector con el número de vectores por modelo
-Resto: Matriz con todos los vectores característicos de entrenamiento
	
	Datos Salida y formato
Combinando estas dos últimas estructuras (vector y matriz) en recuperación de datos obtendremos el equivalente a un vector de matrices, donde cada matriz corresponde a un modelo o clase y aprovechando a su vez, toda la potencia de matlab para matrices.
salida:
modelosHOG.bin Elapsed time is 759.279714 seconds.
modelosHIST.bin Elapsed time is 72.345675 seconds.
modelosHISTBLOC.bin Elapsed time is 283.444886 seconds.
ejemplo:
[230 12 28 4 2237 19 9 16 22] % vector con número de elementos por clase
<2577x256 double> % matriz double precision con todas las características de entrenamiento.

2. Classificador
	Datos de Entrada y formato
Los datos recibidos son un archivo .bin que contiene 1 vector de cada imagen que contiene los descriptores de cada imagen.
leemos los datos y los pasamos a una matriz. 

por otro lado leemos las imágenes que queremos clasificar extraemos sus características (descriptor) y creamos un struct con los nombres, las id de las fotográficas que vamos a clasificar.

El formato de los datos de entrada es de un vector  , un vector por foto, normalizado entre 0 y 1.

	Funciones Utilizadas (breve descripción) 
	[nombre,n_model,H]=lecturaImatges_Aval(directori);
	1.	Lee las imágenes que deseas clasificar en el directorio donde están guardadas.
	2.	saca el descriptor de cada imagen creando un vector  por imagen
	3.	Indexa el vector en una matriz  (n_model) con el objetivo de que esta matriz tenga todos los vectores de todas las fotos (1 vector 1 foto) .
	4.	coge la id de cada imagen y la guarda en un struct (nombre)

	indice=classificador_knn(n_model(i,:),auxmat,index,k);

	1.	entra un vector de las nuevas imágenes a clasificar (n_model(i,:))
	2.	entra la matriz con todos los vectores de las fotos entrenadas auxmat
	3.	entra índex que contiene el numero de fotos que hay de cada tipo index
	4.	entra el valor de k, el numero de distancias más pequeñas que se desean, más tarde comparar.
	5.	dentro de la función:
 distancia=sum(abs(x-vNuevo));
 matriu_diferencia(i)=distancia;

saca la distancia euclidian y la pone en vector, que contiene la comparación de una imagen con todas las entrenadas, busca las K más pequeñas, y de esas K más pequeñas, el valor más repetido.

6. saca el indice de la carpeta de la cual forma parte.

	Descripción del main

	1.	lectura de las imágenes nuevas, 
	2.	crea una matriz con todos los descriptos de la imagen, 
	3.	clasifica con kan las imágenes con las imágenes de entreno
	4.	con el valor del clasificador sabemos que tipo de evento/no evento tenemos
	5.	crea un fichero .txt
	6.	con la id de la fotografia y el descriptor del evento

	Datos Salida y formato

los datos de salida es un TXT con nombre de foto ( el id) espacio el tipo de foto clasificada
 324083101090070769_226937959 concert

3.Avaluador

	Datos de Entrada y formato
Los datos de entrada son en . Txt y las leemos con la función csvimport ( '') ;

	Funciones Utilizadas 
	csvimport ( ) ;
Importa los archivos . Csv

	[id clase ] = spacecuter ( ) ;
Separa la Identidad y la clase .

	x = encuentra ( ID1 , ID2 ) ;
Compara si los nombres de las fotos son iguales o no, y guardando en x las veces que ha encontrado.

	OUT = evaluado ( evento ) ;
Se le asigna al evento encontrado un n º , para poder utilizar confusionmat ( ) ;

	[ C , order ] = confusionmat ( g1 , g2 )
Crea la matriz de confusión , con los valores guardados en g1 y g2 .

	uitable ( )
Crea las tablas a partir de la matriz de confusión y los valores calculados de la Precision , recall y fscore .

	Mihat = MutualInfo ( C1 , C2 )
Se le entran las matrices de confusión y sale el cálculo del NMI .

http://www.cad.zju.edu.cn/home/dengcai/Data/code/MutualInfo.m

	Descripcion del main.
Primero rellenamos la base de datos con la verdad terreno de todas las imágenes. Después cargamos el archivo con las imágenes clasificadas del clasificador .
Después hacemos dos fors consecutivos para comparar la identidad de la imagen clasificada en la base de datos , a fin de encontrar el tipo de evento correcto. Que luego lo guardamos en las variables IN y OUT .
A continuación creamos la matriz de confusión .
Y luego calculamos la Precisión , Record y la F1score .
Por último mostramos en tablas los resultados y también hacemos las gráficas correspondientes .

	Datos Salida y formato
Tablas y gráficas con los valores del resultado
