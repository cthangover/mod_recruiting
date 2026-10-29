# Recruiting — enemigos derrotados que se unen al grupo

Recruiting permite capturar a un enemigo vencido en combate para que más tarde luche del lado del jugador. Tras una victoria, cada enemigo derrotado tiene una probabilidad de ser capturado; los personajes capturados aparecen en el panel **Reclutas**, y cualquiera de ellos puede añadirse al grupo o quitarse de nuevo. Mientras está capturado, un recluta vive según sus propias reglas — en Cthangover se queda con hambre.

El mod no depende de ningún sistema de combate: escucha el evento de "personajes derrotados" de la plataforma, así que funciona con FF Battle, Card Battle o cualquier otro que informe de los personajes derrotados. Quién puede ser capturado, y bajo qué condiciones, lo deciden los datos del juego y otros mods.

## El panel de Reclutas

Se abre con su botón de la barra de herramientas. Al comienzo de Cthangover todavía no hay nadie capturado:

![El panel de Reclutas: el título Reclutas, la línea "Enemigos capturados · cualquiera puede unirse al grupo", "Todavía no hay reclutas capturados" y abajo "Capturados: 0 · en el grupo: 0".](assets/recruits-panel.jpg)

Cada recluta capturado recibe una fila con su retrato, nombre y salud, y un botón: **Añadir al grupo** o **Quitar del grupo**. El pie cuenta cuántos reclutas hay y cuántos están en el grupo.

## Cómo ocurre una captura

Tras una victoria, para cada enemigo derrotado:

1. El archivo de personaje del enemigo debe tener un `RecruitmentChance` mayor que 0.
2. Solo un recluta de cada tipo de enemigo: si ese tipo ya está capturado, se salta.
3. Cada **condición** registrada debe permitirlo. Este mod trae una: el jugador debe tener la habilidad `slave_master` (en Cthangover Marao la tiene desde el principio).
4. Una tirada de 1 a 100 no debe superar la probabilidad.

Entonces el enemigo entra en la plantilla con 10 de salud. En Cthangover el único personaje que puede capturarse aparece más adelante en la historia, con una probabilidad del 50 %:

```json
{ "Id": "werewolf_girl_1", "RecruitmentChance": 50 }
```

## En el grupo

**Añadir al grupo** convierte al recluta en un miembro normal del grupo: aparece en el panel de personajes, pelea en los combates con sus propias acciones, puede ser objetivo de objetos y se guarda con el grupo. La salud perdida en combate se queda con el recluta. **Quitar del grupo** conserva sus acciones y experiencia para la próxima vez que se una.

## Las necesidades de los reclutas

Cada recluta ejecuta los **comportamientos** registrados en cada tic del reloj del juego. Este mod trae uno, la nutrición: un recluta que lleva 24 horas de juego sin comer pierde 1 de salud por tic. En el juego actual todavía no hay nada que alimente a un recluta, así que uno capturado empieza a pasar hambre un día después de la captura.

La plantilla, la salud y el estado de los comportamientos se guardan con el juego.

## Para autores de mods

- Una **condición** es una clase que implementa `IRecruitCondition`; el mod recruiting la encuentra en cualquier mod cargado. Devuelve false para prohibir una captura.
- Un **comportamiento** implementa `IRecruitBehaviour`: se configura cuando se captura un recluta, se llama en cada tic y se elimina con el recluta. Su estado vive en las propiedades del recluta.
- El código puede reaccionar a `RecruitJoinedEvent` y `RecruitLeftEvent`.

## Si algo falla

1. **Nunca se captura a nadie.** El `RecruitmentChance` del enemigo es 0, una condición lo prohíbe (falta la habilidad `slave_master`) o la tirada falló.
2. **El mismo enemigo no se captura dos veces.** Un recluta por tipo de enemigo, a propósito.
3. **La salud de un recluta no deja de caer.** Es la nutrición: lleva un día sin comer y todavía no hay forma de alimentarlo.
