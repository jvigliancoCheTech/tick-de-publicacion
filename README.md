# tick-de-publicacion

El reloj de un panel privado. Este repositorio tiene **un solo archivo con lógica**:
un workflow de GitHub Actions que cada 5 minutos le pega a un endpoint y verifica
que haya contestado `200`.

No hay acá ninguna parte del panel, ni datos, ni credenciales. La única credencial
—`CRON_SECRET`— vive en los *repository secrets* de Actions, cifrada, y nunca en un
archivo. Un fork no la recibe: su primera corrida se detiene sola en el fusible que
verifica que el secreto exista.

## Por qué vive afuera

El panel está en Vercel con plan Hobby, que **sólo admite cron diario** — un
`*/5` en `vercel.json` ni siquiera deja desplegar. Y el repositorio del panel es
privado y de una organización, donde los minutos de Actions se descuentan de una
cuota. Un repositorio público no consume cuota, así que el reloj vive acá.

## Lo que hay que saber si lo tocás

- **El período está acoplado a una constante del panel.** El `*/5` de acá y el
  `PERIODO_DEL_TICK_MS` de allá tienen que decir lo mismo: la ventana de tolerancia
  se deriva del período. Cambiar uno sin el otro fabrica publicaciones vencidas.
- **GitHub no promete puntualidad.** Las corridas por `schedule` se atrasan y, bajo
  carga, se descartan. Por eso la ventana es de 25 minutos y no de 5.
- **GitHub apaga por inactividad los workflows de repositorios públicos a los 60
  días.** Cuando pasa no hay corrida roja: hay *ausencia* de corridas. Hay que
  entrar a reactivarlo.
- **Este workflow no despliega nada.** El despliegue del panel es a mano.
