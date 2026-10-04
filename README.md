# Backups That Don't Lie

> Un backup se mide por la edad de su contenido, no por la de su archivo.

Este repositorio documenta, de forma sanitizada, la estrategia de backup y
recuperacion de una infraestructura productiva personal (homelab), junto con tres casos reales: presion de
storage y postura de recuperacion, eventos de backup tratados como evidencia
de seguridad, y una migracion de estacion de trabajo que revelo que la
cadena de respaldos llevaba meses rota sin que nadie lo supiera.

Es parte de un portfolio tecnico. **No es un laboratorio de prueba: es
infraestructura productiva.** No tiene la escala de una empresa, pero tiene
todas sus piezas -virtualizacion, red segmentada, DNS, almacenamiento, backups
con copia externa, monitoreo, SIEM, acceso remoto y aplicaciones en uso- y
funciona 24/7 sobre un hipervisor de tipo 1 en un servidor dedicado. Cuando
algo falla, el impacto es real.

La documentacion operativa es privada. Esto es su version transformada
-decisiones, patrones y aprendizajes-, sin datos que permitan identificar o
reproducir el entorno.

Lo que busca demostrar: el principio de que un backup se mide por la edad de
su contenido y no por la de su archivo, la practica de probar la
restauracion en vez de confiar en que el backup "corrio", y la honestidad
para documentar una falla de meses -y como se corrigio- en vez de
esconderla.

## Escala chica, exigencia de produccion

| Pieza | Que hace | Si falla |
|---|---|---|
| Virtualizacion | hipervisor de tipo 1, una maquina por funcion | cae todo lo demas |
| DNS interno | resolucion para todos los equipos y servicios | todo parece caido aunque este sano |
| Red y acceso remoto | zonas por funcion, malla sin puertos abiertos | se pierde el aislamiento o el acceso desde afuera |
| Almacenamiento y backups | NAS, backups nocturnos, copia cifrada externa, pruebas de restauracion | se pierde la capacidad de recuperar |
| Monitoreo, SIEM y alertas | metricas, eventos de seguridad, avisos al telefono | los incidentes pasan sin que nadie se entere |
| Aplicaciones propias | en uso diario; una envia correo real | se frena trabajo real |
| Remoto de codigo | versionado y despliegue de esas aplicaciones | no hay donde versionar ni desde donde desplegar |

Lo mismo que en una empresa, en chico: cambios con plan y rollback, evidencia,
alertas que avisan solas y controles que se prueban haciendolos fallar.

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

## Problema, decision, resultado

| Problema | Por que importaba | Que se hizo | Resultado |
|---|---|---|---|
| Un respaldo llevaba casi 4 meses congelado y la metrica decia "horas" | se habria restaurado algo viejo creyendo que era de ayer | medir la edad del contenido, no la del archivo | ya no puede informar exito sobre datos viejos |
| La copia fuera del sitio fallo varios dias sin avisar | la proteccion externa era una ilusion | aviso enganchado a la salida del proceso, que ocurre siempre | toda falla avisa |
| Formatear la estacion de trabajo daba miedo: nadie sabia que se perdia | historial de meses en un solo disco | migracion en fases, cuarentena de 30 dias y prueba en maquina limpia | rearmado probado leyendo solo el documento |

El detalle de cada uno, con lo que salio mal en el camino, esta en los casos de estudio.

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
