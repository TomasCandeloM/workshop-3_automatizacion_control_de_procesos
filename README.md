# workshop-3_automatizacion_control_de_procesos

## Integrantes 

Tomas Candelo Montoya

Carlos Farouk Abdalá Rincón

**** 
## Diseño del circuito logico para el proceso automatizado 

En este documento exploraremos el proceso de desarrollo que se siguio para llegar a la solución final para un proceso de autoregulación de un tanque donde tenemos 3 sensores a lo largo de este, con base a las señales dadas por estos sensores de entrada, el sistema informara de la situación del agua en el tanque para que los trabajadores de la planta puedan reaccionar y tomar deciciones frente a esto. 

Una vez definido el proceso el diseño, pasamos a la etapa del diseño lógico de la solución, para esto podemos determinar que el sistema sigue una logica combinatoria, donde las diferentes combinaciones de entradas, generan diferentes respuestas en las salidas del mismo, por lo que para realizar el diseño de la logica lo primero fue definir claramente cuales son las entradas y salidas que se ven involucradas en el proceso.
- Sesnsor que se encuentra en la parte baja del tanque **(input)**
- Sensor que se encuentra en la altura minima a la que deberia estar el tanque  **(input)**
- Sensor que se encuentra en la parte superior del tanque **(input)**
- Indicador de exito en el nivel del tanque **(output)**
- Indicador de que el nivel del tanque esta bajo **(output)**
- Indicador de que el nivel del tanque esta muy alto **(output)**
- Indicador de que el tanque esta vacio **(output)**
- Indicador de que hay un error en la medición del tanque **(output)**

### diseño logico 


Con las entradas y salidas determinadas, el siguiente paso fue realizar una tabla de verdad, esto fue nuestra base para realizar la logica combinacional del sistema, ya que este es el metodo adecuado para realizar el diagrama logico. 

La tabla de verdad resultante fue la siguiente: 

![TABLA DE VERDAD PARA EL DISEÑO](Image/tabla_de_verdad.png)

aca podemos ver los 5 posibles casos: 
- el tanque esta en el nivel de agua adecuado: **sensor 1 y 2** enviando señal
- el tanque tiene un nivel de agua muy bajo: **sensor 1** enviando señal
- el tanque tiene un nivel de agua muy alto: **los 3 sensores** envian señal 
- el tanque esta vacio: **ningun sensor** envia señal 
- el tanque presenta un error: **combinación de sensores que no tiene sentido (solo 2, solo 3, 1 y 3 o 2 y 3)** envian señal

Con la tabla de verdad podemos obtener las funciones que representan cada uno de los casos segun que condiciones tienen en la entrada, los primeros 4 casos dan funciones directas ya que unicamente existe una configuración que dara la salida deseada, mientras que para el caso de error se tienen que evaluar 4 diferentes combinaciones de condiciones inciales para dar con el output deseado, las funciones con compuertas logicas quedarian de esta manera:

![COMPUERTAS LOGICAS DEL DISEÑO](Image\compuertas_logicas.png)


Cabe resaltar que estas funciones fueron obtenidas con el apoyo de la pagina web 32x8, proporcionada por el profesor, a excepción de la función para los casos de error esto debido a que como grupo tomamos la desición de implentar esta función de una manera diferente a la que probablemente usarian los demas equipos de trabajo. 

En este caso lo que se hizo fue calcular los posibles casos a mano y a partir de ahi simplificar para obtener una nueva logica que cumpliese con la misma función, la nueva función a implementar en los casos de errore es la siguiente: 

>$$ B_2 ( \overline{B_1} B_3 + \overline{B_1} \overline{B_3}) + \overline{B_2} (B_1 B_3 + \overline{B_1} B_3)$$




### diseño Ladder

Ahora con las funciones representadas en compuertas lógicas, podemos "traducir" estas compuertas a rieles y contactos para representar el sistema en una logica Ladder, para esto utilizamos los conocimientos vistos en clase sobre como es la representación de una compuerta AND, OR y XOR, que son las 3 compuertas logicas usadas, en lenguaje Ladder. El diseño fue implementado en codesys para hacer el primer diseño de este y la simulación del HMI.

## Implementación en Codesys

Con el diseño Ladder listo psamos al siguiente paso del workshop el cual consiste en la implementación y simulación de nuestro diseño en el software de codesys, para este apartado se realizaron dos actividades principales, realizar el esquematico Ladder y realizar un HMI que represente como seria la interacción con este sistema automatizado. 

### Esquematico Ladder del diseño

Primero, una vez estamos en el software, en el plano de PLC_PRG se añadio un primer contacto para asi poder cerar las variables globales que seran necesarias para la implementación del diseño, ademas de las ya mencionadas anteriormente aqui se crearon variables adicionales que seran necesarias para la implementación y simulación del diseño 
- **variable START:** Esta sera usada para dar inicio como tal al proceso de medición del nivel de agua 
- **variable STOP:** Esta sera usada para detener el proceso cuando este en ejcución 
- **Variable IR_1:** Este es un relé que se utilizo para guardar el estado del sistema cuando este inicie, si este relé se encuentra activo permitira que el sistema de indicadores se mantenga activo hasta que se accione el boton de stop. 

Una vez se crearon las variables se construyo el esquematico en forma, para este se realizaron un total de 6 redes diferentes, una para el funcionamiento del inicio y parada del sistema y las otras 5 para cada uno de los casos de los indicadores. 

En la primer red simplemente se hace el esquematico que se ve en la imagen 

![DISEÑO LADDERR DEL INCIO DEL SISTEMA](Image\red1_codesys.png)

donde lo unico a destacar es la teroalimentación del relé IR_1 que garantiza que el sistema se manenga encendido hasta que se active el boton de parar.

Las siguientes 5 redes quedaron en el software como se ve en las siguientes imagenes:

![DISEÑO LADDER DEL SISTEMA DE CONTROL DEL TANQUE DE AGUA](Image\red_codesys.png)


En todos estos casos el primer contacto que tienen es el del relé ya que con esto garantizamos que muestre los indicadores unicamente cuando el sistema esta activo, lo cual se trasporta a la vida real en el sentido de que a menos que la maquinaria este prendida, los sensores pueden seguir midiendo pero no seran procesados ni generaran una salida del sistema, ya que no esta activo este mismo. 

### simulación con un HMI 
una vez el esquematico del Ladder estaba listo, se añadio un objeto de visualización para el diseño de la simulación con uso de un HMI (Human-Machine interface) el cual consiste en una interfaz que permita demostrar como seria la interacción humano maquina para el sistema que se desea implementar o simular. 

Como el sistema que se desarrollo consistia en el control del nivel de agua en un tanque, lo que optamos a desarollar como HMI seria un conjunto de figuras que se asimilara a un panel de control de una fabrica en el centro de control de la misma. 

El resultado final del HMI fue el siguiente: 

![DISEÑO HMI DE LA SIMULACIÓN DEL SISTEMA](Image\HMI.png)

En este podemos ver una semejanza a los centros de control de una fabrica, donde tenemos una visualización del tanque a las izquierda y los indicadores a la derecha, en la parte inferior del panel tenemos la palanca que activa el sistema y el botón de pausa, fuera de este panel de control encontramos 3 botos los cuales representaran los sensores del tanque, puesto que este software no cuenta con sensores para simular el proceso. 

En la vizualización del tanque lo que se hizo fue una animación de los diferentes niveles de agua que puede llegar a tomar el tanque en función de lo que esta midiendo el sensor, para dar una representación visual de cual es el estado actual que tiene el tanque al momento de la medición, además de representar de mejor manera los casos donde se presenta un error en el mismo, cabe resaltar que estos cambios en el tanque se pueden ver aunque no este encendido el sistema, esto ya que, en la vida real, los tanques y elementos de un sistema siguen trabajando y funcionando aunque su control automatizado este apagado, sin embargo, los indicadores claramente no estan activos hasta que se active la solución. 

Una vez creada esta interfaz y la animación del tanque lo ultimo que se realizo previo a la prueba fue la asignación de variables a los diferentes elementos del sistema. Los 3 botones de arriba como ya fue mencionado, son los que representaran los sensores del tanque permitiendo que puedan ser activados o desactivados segun la condición que se le quiera dar al sistema. Los indicadores por otro lado, fueron representados en los circulos grises donde, cuando se daban las condiciones de entrada adecuadas, se cambiaba el color segun lo que se deseara informar a los operadores frente al funcionamiento del tanque.

Con todo listo se realizo la simulación del codesys con el HMI y los resultados obtenidos fueron los siguientes:

![RESULTADO DE LA SIMLACION INDICACOR VACIO](Image\HMI_VACIO.png)

![RESULTADO DE LA SIMLACION INDICACOR BAJO](Image\HMI_BAJO.png)

![RESULTADO DE LA SIMLACION INDICACOR ALTO](Image\HMI_ALTO.png)

![RESULTADO DE LA SIMLACION INDICACOR CORRECTO](Image\HMI_CORRECTO.png)

![RESULTADO DE LA SIMLACION INDICACOR ERROR](Image\HMI_ERROR.png)


## Implementación del sistema en openPLC

Una ves desarrollamos y comprobamos el diseño en Codesys, pasamos al desarrollo del sistema en el software de openPLC, esto con dos fines, el primero realizar nuevamente una prueba y validación del funcionamiento del sistema e implementar el sistema en un prototipo físico para probar y demostrar el funcionamiento del mismo con componentes reales. 

### diagrama Ladder 

Primero que nada tuvimos que replicar el diagrama Ladder en openPLC para posteriormente hacer uso de el en las siguientes pruebas, para implementarlo se realizo el mismo proceso que en codesys con la diferencia de que en este software todos los contactos van al mismo riel.

Se crearon las variables y luego sueron asignadas segun correspondia a la lógica a implemntar, Finalmente el diagrama Ladder del sistema que integra todas las condiciones en un solo riel fue el siguiente:

![Diagrama Ladder implementado en openPLC](Image\Ladder_openPLC.png)

Con el diagrama creado se realizo una nueva simulación forzando los valores de **True** o **False** en los contactos con el único fin de verificar que la implementación en openPLC se haya realizado correctamente, en este caso todo fuando de acuerdo a lo establecido.


