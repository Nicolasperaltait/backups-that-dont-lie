# Caso de Estudio - Presion de Storage y Postura de Recuperacion

## Contexto

Al principio, maquinas virtuales activas, backups e imagenes de instalacion
compartian el mismo disco del hipervisor (Proxmox VE).

## Problema

El uso del storage principal se acerco al 80 %. Habia archivos de backup, pero
la pregunta real era otra: **si manana falla algo, puedo recuperar?**

## Por que importaba

Con todo en el mismo disco, un backup grande podia dejar sin espacio a las
maquinas en produccion a mitad de la noche, y una falla de ese disco se llevaba
a la vez el servicio y su copia.

## Decision

| Opcion | Resultado |
|---|---|
| Borrar backups viejos para ganar espacio | descartada: alivia hoy y reduce la capacidad de recuperar |
| Separar por rol en discos distintos | **adoptada** |

- Storage principal reservado a los discos de las maquinas activas.
- Storage de soporte para backups, imagenes y el NAS.
- **Si no hay espacio minimo, el backup no corre** y la falla se ve: es
  preferible a llenar el disco de produccion.
- Retencion definida por dominio, no "lo que entre".

## Que salio mal en el camino

- Borrar dentro de una maquina virtual no liberaba espacio afuera: un snapshot
  olvidado retenia los bloques. Desde entonces todo snapshot nace con fecha de
  retiro (detalle en el caso 03).
- Archivos que parecian duplicados eran discos en uso: se revisaron referencias
  antes de borrar (detalle en Hypervisor as Control Plane).

## Validacion

- Salud del storage confirmada despues del cambio.
- Backups recientes y legibles, abriendolos, no solo contandolos.
- Estado de la copia externa confirmado.
- Pruebas de restauracion con RTO y RPO medidos.

## Resultado

| Antes | Despues |
|---|---|
| VMs, backups e imagenes en el mismo disco | Storage separado por rol |
| Un backup podia llenar produccion | El backup se frena antes y avisa |
| "Hay archivos de backup" | Restauracion probada con tiempos medidos |

## Leccion

**La madurez de un backup no se mide por cuantos archivos hay, sino por cuanto
tarda en volver lo que se rompio.**
