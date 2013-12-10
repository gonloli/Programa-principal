Copyright (c) [2013] [Ramon Franquesa Alberti, Carlos Martín Isla , Gonzalo Lopez Lillo , Aleix Gras Godoy ]
Projecte Detecció d'Esdeveniments
El codi proporcionat serveix per classificar un conjunt d'imatges segons el tipus d'esdeveniment al qual pertanyen.

A continuació un breu resum d'explicació del codi

1. Descriptor
	
	Entrada:
L'entrada consisteix en una sèrie de directoris de nom event_id amb les fotos ordenades respecte a veritat terreny, que al seu torn han estat generades per una funció que llegeix la veritat terreny (csv separat per tabs) i distribueix les imatges en aquests directoris.

	Funcions Utilitzades (breu descripció)

	•	model = LecturaImatges (dir, flag)
Extreu característiques de les imatges d'un directori i les emmagatzema en una matriu. Cada fila és un vector característica d'una imatge d'entrenament.
si flag = 0, extreu histogrames luminància, i si és 1, extreu HOG.

	•	vector = HistogramaBlocs (I)
Histograma per blocs 64 blocs i concatena els histogrames un darrere l'altre

	•	H = histograma (im)
Histograma luminància de 256 bins. Funciona mitjanament bé per concerts. Aquesta va ser la idea original.

	•	H = HOG2 (im)
HOG.16 blocs per imatge. 16 bins per bloc. Extreu textures i formes en concret la direcció de cada píxel (arctan (dx, dy)). Després, elabora un histograma de arcotangentes per cada bloc.
http://www.mathworks.com/matlabcentral/fileexchange/28689-hog-descriptor-for-matlab/content/HOG.m

	Descripció del main

	1.	Es neteja i es crea una llista amb els event_type
	2.	Es recuperen les imatges de cada directori / event_type del projecte i s'extreu la característica en cada recuperació.
	3.	Es concatenen totes les característiques i s'exporten a un fitxer bin:
-Primera fila: vector amb el nombre de vectors per model
-Resta: Matriu amb tots els vectors característics d'entrenament

	Dades Sortida i format

Combinant aquestes dues últimes estructures (vector i matriu) en recuperació de dades obtindrem l'equivalent a un vector de matrius, on cada matriu correspon a un model o classe i aprofitant al seu torn, tota la potència de matlab per a matrius.
sortida:
modelosHOG.bin Elapsed time is 759.279714 seconds.
modelosHIST.bin Elapsed time is 72.345675 seconds.
modelosHISTBLOC.bin Elapsed time is 283.444886 seconds.
exemple:
[230 12 28 4 2237 19 9 16 22]% vector amb nombre d'elements per classe
<2577x256 double>% matriu double precisió amb totes les característiques d'entrenament.

2. Classificador
	
	Dades d'Entrada i format

Les dades rebudes són un arxiu. Bin que conté 1 vector de cada imatge que conté els descriptors de cada imatge.
llegim les dades i els passem a una matriu.

d'altra banda llegim les imatges que volem classificar extraiem les seves característiques (descriptor) i vam crear un struct amb els noms, les id de les fotogràfiques que anem a classificar.

El format de les dades d'entrada és d'un vector, un vector per foto, normalitzat entre 0 i 1.

	Funcions Utilitzades (breu descripció)

	•	[Nom, n_model, H] = lecturaImatges_Aval (directori);

Llegeix les imatges que vols classificar en el directori on estan guardades.
treu el descriptor de cada imatge creant un vector per imatge
Indexa el vector en una matriu (n_model) amb l'objectiu que aquesta matriu tingui tots els vectors de totes les fotos (1 vector 1 foto).
agafa la id de cada imatge i la guarda en un struct (nom)

	•	index = classificador_knn (n_model (i, :), auxmat, index, k);

	1.	entra un vector de les noves imatges a classificar (n_model (i, :))
	2.	entra la matriu amb tots els vectors de les fotos entrenades auxmat
	3.	entra índex que conté el nombre de fotos que hi ha de cada tipus index
	4.	entra el valor de k, el nombre de distàncies més petites que es desitgen, més tard comparar.
	5.	dins de la funció:
distància = sum (abs (x-vNuevo));
matriu_diferencia (i) = distància;

treu la distància euclidian i la posa en vector, que conté la comparació d'una imatge amb totes les entrenades, busca les K més petites, i d'aquestes K més petites, el valor més repetit.

	1.	treu l'índex de la carpeta de la qual forma part.

	Descripció del main

lectura de les imatges noves,
crea una matriu amb tots els descrits de la imatge,
classifica amb kan les imatges amb les imatges d'entrenament
amb el valor del classificador sabem quin tipus d'esdeveniment / no esdeveniment tenim
crea un fitxer. txt
amb la id de la fotografia i el descriptor de l'esdeveniment

	Dades Sortida i format

les dades de sortida és un TXT amb nom de foto (l'id) espai el tipus de foto classificada
324083101090070769_226937959 concert

3. Avaluador

Dades d'Entrada i format

Les dades d'entrada són en. Txt i les llegim amb la funció csvimport ('');

Funcions Utilitzades

	•	csvimport ();
Importa els fitxers. Csv

	•	[Id classe] = spacecuter ();
Separa la Identitat i la classe.

	•	x = troba (ID1, ID2);
Compara si els noms de les fotos són iguals o no, i guardant en x les vegades que ha trobat.

	•	OUT = avaluat (esdeveniment);
Se li assigna l'esdeveniment trobat un n º, per poder utilitzar confusionmat ();

	•	[C, order] = confusionmat (g1, g2)
Crea la matriu de confusió, amb els valors guardats en g1 i g2.

	•	uitable ()
Crea les taules a partir de la matriu de confusió i els valors calculats de la Precision, recall i fscore.

	•	Mihat = MutualInfo (C1, C2)
Se li entren les matrius de confusió i surt el càlcul del NMI.

http://www.cad.zju.edu.cn/home/dengcai/Data/code/MutualInfo.m

	Descripció del main.

Primer omplim la base de dades amb la veritat terreny de totes les imatges. Després carreguem l'arxiu amb les imatges classificades del classificador.
Després fem dues fors consecutius per comparar la identitat de la imatge classificada a la base de dades, per tal de trobar el tipus d'esdeveniment correcte. Que després ho guardem en les variables IN i OUT.
A continuació vam crear la matriu de confusió.
I després calculem la Precisió, Record i la F1score.
Finalment vam mostrar en taules els resultats i també fem les gràfiques corresponents.

Dades Sortida i format

Taules i gràfiques amb els valors del resultat
