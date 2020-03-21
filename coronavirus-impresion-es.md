# Impresión 3D en relación con la epidemia de coronavirus

El sitio donde se está coordinando la mayor parte de la actividad maker en relación con la situación sanitaria causada por la epidemia de coronavirus es [Coronavirus Makers](https://www.coronavirusmakers.org/index.php/es/). En él puede encontrarse una lista extensa de grupos especializados en elementos concretos (respiradores, mascarillas, protectores faciales, etc.), y de coordinación de las distintas comunidades autonómicas. En general son grupos de Telegram.

Como tengo una impresora 3D, y vivo en Madrid, me he apuntado a los siguientes grupos:

* El grupo fundamental es [CV19_INFO_MADRID](https://t.me/cv19_fab_info), canal moderado donde sólamente se reciben unos pocos mensajes al día con la información fundamental (aconsejo empezar por este canal si alguien se acaba de apuntar a este jaleo). Incluye un formulario para indicar los materiales de que se dispone (piezas 3D que se han podido imprimir, y otros materiales auxiliares útiles).

* Grupo de fabricación [CV19_FAB_MADRID](https://t.me/joinchat/Ec-3Ih0C2Wzr7OBkqfiEUQ) (canal de Telegram). El sábado 21 de marzo somos ya más de 2100 miembros en ese canal, y tiene bastante tráfico. Es el sitio donde hacer preguntas (y sobre todo hace falta gente que ayude a responderlas) sobre fabricación con impresora 3D, por ahora (21 de marzo) viseras para protección facial.

* Grupo de logística [CV19_FAB_MADRID LOGISTICA](https://t.me/joinchat/MI8qJ0vZuLWeJOJlJgMguQ), para coordinarnos sobre cómo enviar lo que se vaya produciendo, y cómo hacerlo llegar a quien mejor lo pueda usar.

El grupo de Madrid está, al menos por ahora, centrado en la impresión de viseras que, complementadas con un un plástico transparente de tamaño A4 (de PVC, por ejemplo de los usados en encuadernación) y según modelo, con una goma de sujección, permite tener un protector facial para usar en combinación con mascarillas y otros elementos. Actualmente estos dispositivos no están certificados para su uso por profesionales sanitarios, pero son útiles para otros sectores que los pueden necesitar (protección civil, policía, distribución, pequeño comercio, etc.). Por lo que veo en los canales, también hay profesionales de la salud que los demandan por no tener nada mejor.

Antes de imprimir, conviene realizar el siguiente test:

* [Flow Calibator](https://www.thingiverse.com/thing:1662342) ([STL](https://cdn.thingiverse.com/assets/43/12/fc/91/0b/flow_calibrator.stl), [copia del STL](covid-data/flow_calibrator.stl)), por si el anterior no te descarga] para hacer una prueba y calibrar adecuadamente, antes de imprimir. Se tarda unos 6 min en imprimir en mi Anet8. Una vez impreso, el cilindro ha de entrar en la anilla, y atravesarla, lo más justo posible pero sin que se atasque. Si no la atraviesa bien, modifica el parámero "Horizontal Expansion" en Cura. A mi me ha funcionado bien con un valor de -0.15.

Los modelos a imprimir son los que están del canal ["3D printed Face Mask A4 sheet" de Thingiverse](https://www.thingiverse.com/thing:4228123). Después de ver la descripción en este enlace, puedes ver los ficheros en la pestaña de ficheros. Actualmente (21 de marzo), el que está recomendado para imprimir es:

* Visera3.6b_cerrada_mas_simple ([STL](https://www.thingiverse.com/download:7842326), [copia del STL](covid-data/Visera_3.6b_cerrada__mas_simple.stl)). Se recomienda imprimir con los siguientes parámetros (que se pueden ajustar en Cura), para un boquilla (nozzle) de 0.4 mm de diámetro: altura de capa (layer height), 0.28mm; relleno (infill), 50%, sin soporte (con "skirt"), 2 capas de base (bottom layers), 4 capas superiores (top layers). En mi Anet8 tarda unas cuatro horas (si alguien sabe cómo bajar este tiempo que me diga), y he tenido que imprimir con cama caliente porque si no, se me acaba despegando después de hacer unas pocas capas.

(Como Thingiverse está un poco colapsado y a veces tarda mucho en cargar o no carga, incluyo enlaces directos y enlace copia de los diseños STL (pero siempre que puedas, intenta descargarlos de Thigiverse, por si se hubieran actualizado):

Para las pantallas de plástico transparente, se recomienda que sean de PVC O PET (parece que el acetato no aguanta los métodos de desinfección). Se recomeinda para este modelo que sean de 240 micras, las de 180 pueden caerse (pero se puede solucionar con cinta adhesiva, o utilizar el refuerzo diseñado al efecto ([fijación láminas.stl](https://cdn.thingiverse.com/assets/90/9a/52/09/6a/fijacion_laminas_finas_Visera_3.5.2.stl)) para la sujeción. 


## <a name="license">Licencia y contribuciones</a>

Este documento se distribuye bajo la licencia
[Creative Commons Attribution-ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-sa/4.0/)
(CC BY-SA 4.0) y es una traducción del [original](https://github.com/jgbarah/Notes/foss-distance-learning.html)

Todo el contenido original de este documento se puede encontrar en un
[repositorio de GitHub](https://github.com/jgbarah/Notes/).

Si quieres ves cualquier error, quieres proponer cualquier mejora, o tienes un comentario, por favor
[abre un *issue* en este repositorio](https://github.com/jgbarah/Notes/issues/new),
o [envía una *pull request* al mismo](https://github.com/jgbarah/Notes/pulls).

