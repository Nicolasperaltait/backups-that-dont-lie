# Caso de Estudio - Codigo que Sobrevive

## Contexto

Todo el codigo de la infraestructura y de las aplicaciones propias -scripts,
gobernanza, apps en produccion- vive en un remoto Git privado y autoalojado
(Forgejo), con integracion continua. Es una pieza de produccion: sin el, no hay
donde versionar ni desde donde desplegar.

## Problema

Al medir que se perderia si se formateaba la estacion de trabajo, aparecio que
**la mayoria de los repositorios no tenia ningun remoto**: meses de historial en
un solo disco.

## Por que importaba

Un disco que falla se lleva el historial completo, y con el, el registro de por
que se tomo cada decision. Y un remoto propio resuelve eso solo a medias: si vive
en el mismo sitio, una perdida del sitio se lleva las dos copias.

## Decision

Proteccion en capas, cada una contra una falla distinta:

| Capa | Protege contra | Estado |
|---|---|---|
| Remoto privado autoalojado con CI | perdida o reinstalacion de la estacion de trabajo | operativo |
| Clones en la estacion de trabajo | perdida del servidor | operativo |
| Backup de la maquina virtual del remoto | corrupcion o error en el remoto | operativo |
| **Copia en frio a disco externo, desconectado** | perdida del sitio y ransomware | estrategia decidida |

**Por que en frio y no otra nube:** una copia desconectada no la alcanza un
atacante ni un error que se propaga solo. Para codigo, que pesa poco, es la
opcion mas simple y la mas dificil de romper.

## Que salio mal en el camino

- **Archivar no es borrar.** Al limpiar, cuatro repositorios de proyectos dados
  de baja parecian descartables. Sus copias locales estaban en cuarentena: al
  vencer, el remoto iba a ser la **unica** copia de ese historial. Se archivaron
  en vez de borrarse.
- **Un duplicado se verifico, no se supuso.** Se borro un solo repositorio, y
  solo despues de comprobar **commit a commit** que su contenido estaba integro
  en otro. El parecido de los nombres no era evidencia.

## Validacion

- Cada repositorio migrado se **clono desde otra maquina** y su historial se
  comparo con el original.
- La integracion continua corre en cada commit: un cambio que rompe los tests
  queda a la vista en el remoto.

## Resultado

| Antes | Despues |
|---|---|
| Historial de meses en un solo disco | Remoto privado con CI y clones en otra maquina |
| "Repositorio viejo" = borrable | Archivado: nunca se borra la ultima copia |
| Un remoto en el mismo sitio presentado como respaldo | Capas separadas por tipo de falla, con copia en frio en la estrategia |

## Leccion

**Un remoto en el mismo sitio no es una copia fuera del sitio.** Cada capa de
proteccion tiene que responder a una falla distinta; si dos capas caen juntas,
son una sola.
