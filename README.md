# Backups That Don't Lie

> Un backup se mide por la edad de su contenido, no por la de su archivo.

Este repositorio documenta, de forma sanitizada, la estrategia de backup y
recuperacion de una infraestructura productiva personal (homelab), junto con tres casos reales: presion de
storage y postura de recuperacion, eventos de backup tratados como evidencia
de seguridad, y una migracion de estacion de trabajo que revelo que la
cadena de respaldos llevaba meses rota sin que nadie lo supiera.

Es parte de un portfolio tecnico. **No es un laboratorio de prueba**: es una
**infraestructura productiva personal**. Un hipervisor de tipo 1 sobre un
servidor dedicado, encendido 24/7, del que dependen todos los dias la red de la
casa, los backups, la seguridad y aplicaciones en uso real. Si se apaga, se nota.

La documentacion operativa es privada. Esto es su version transformada
-decisiones, patrones y aprendizajes-, sin datos que permitan identificar o
reproducir el entorno.

Lo que busca demostrar: el principio de que un backup se mide por la edad de
su contenido y no por la de su archivo, la practica de probar la
restauracion en vez de confiar en que el backup "corrio", y la honestidad
para documentar una falla de meses -y como se corrigio- en vez de
esconderla.

## Por que es infraestructura productiva

| Servicio que corre 24/7 | Que pasa si se cae |
|---|---|
| DNS de toda la red de la casa | ningun equipo resuelve nombres: para quien la usa, "se corto internet" |
| Backups nocturnos y copia cifrada fuera del sitio | se pierde la proteccion de los datos y nadie lo nota hasta necesitarla |
| SIEM, metricas y alertas al telefono | los incidentes pasan sin que nadie se entere |
| Acceso remoto por malla | no hay forma de operar desde fuera de casa |
| NAS y espejo de la estacion de trabajo | se corta la sincronizacion de los archivos de trabajo |
| Aplicaciones propias en uso diario | se frena el uso real, incluido el envio de correo |
| Remoto de codigo propio | no hay donde versionar ni desde donde desplegar |

Por eso cada cambio se trata como en produccion: plan, rollback, evidencia y
verificacion de que lo que tiene que fallar, falla.

## En 30 segundos

| Indicador | Resultado |
|---|---|
| Tiempo que un respaldo estuvo congelado mientras la metrica decia "horas" | **casi 4 meses** |
| Eslabones de esa cadena que siguieron corriendo despues del corte | **4 de 5** |
| Recuperacion medida del servicio chico | **segundos** |
| Recuperacion medida del dominio documental | **menos de 2 minutos** |
| Cuarentena antes de borrar cualquier cosa en la migracion | **30 dias**, con manifiesto |

```mermaid
flowchart LR
    T[Tarea programada] -->|apuntaba a un script borrado| X((CORTE))
    X -.-> S[Espejo congelado]
    S --> C[Compresion nocturna]
    C --> H[Hash OK]
    H --> M[Metrica: edad del comprimido]
    M --> OK[Dashboard: respaldo de hace horas]
```

La metrica nueva mide **la edad del archivo mas reciente dentro del
respaldo**: ya no puede informar exito sobre contenido viejo.

## Indice

- [Ficha rapida para quien evalua](contexto.md)
- [Backup y recuperacion](docs/01-backup-y-recuperacion.md)
- [Caso de estudio: presion de storage y postura de recuperacion](docs/casos-de-estudio/01-presion-storage-y-recuperacion.md)
- [Caso de estudio: eventos de backup como evidencia SIEM](docs/casos-de-estudio/02-eventos-backup-como-evidencia-siem.md)
- [Caso de estudio: migracion de workstation y respaldos que mentian](docs/casos-de-estudio/03-migracion-de-workstation-y-respaldos-que-mentian.md)

## Parte de una serie

Este repo es una pieza de un proyecto mas grande: una **infraestructura
productiva personal** (homelab), encendida 24/7 y documentada en cinco repos
independientes. Cada uno se lee solo; juntos muestran el entorno completo.

- [Zero Trust Remote Access](https://github.com/Nicolasperaltait/zero-trust-remote-access)
- [Backups That Don't Lie](https://github.com/Nicolasperaltait/backups-that-dont-lie) (este repo)
- [Alerts That Matter](https://github.com/Nicolasperaltait/alerts-that-matter)
- [Network Segmentation Playbook](https://github.com/Nicolasperaltait/network-segmentation-playbook)
- [Hypervisor as Control Plane](https://github.com/Nicolasperaltait/hypervisor-as-control-plane)

## Licencia

Ver [LICENSE.md](LICENSE.md).
