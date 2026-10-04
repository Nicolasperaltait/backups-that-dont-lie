# Caso de Estudio - Eventos de Backup como Evidencia SIEM

## Contexto

Los backups corren de noche en el NAS (OpenMediaVault) y su estado se ve en
Grafana. Pero un dashboard es para mirar ahora; una auditoria pregunta **que
paso el martes de hace tres semanas**.

## Problema

El estado de cada backup vivia en archivos locales del NAS. Habia alerta
inmediata, pero no **evidencia durable**: si un backup fallaba y despues se
recuperaba, no quedaba registro consultable de la falla.

## Por que importaba

Un backup que fallo y nadie registro es un hueco invisible en la capacidad de
recuperar. Y en seguridad, lo que no tiene evidencia no paso.

## Decision

| Pieza | Como |
|---|---|
| Eventos estructurados | cada corrida de backup escribe una linea JSON con dominio, resultado y error |
| Ingesta | el agente del SIEM (Wazuh) en el NAS lee ese archivo |
| Reglas propias | falla generica de backup con severidad alta; falla del dominio mas critico con severidad critica |
| Separacion | la alerta inmediata va al telefono; la evidencia queda en el SIEM |

**Que se decidio no hacer:** ingerir tambien los backups exitosos. El unico
consumidor de ese evento era un dashboard que ya tiene la metrica; meterlo al
SIEM sumaba volumen sin sumar evidencia.

## Que salio mal en el camino

- Tras una actualizacion del SIEM habia que confirmar que las reglas propias
  seguian cargadas: se agrego como paso fijo del procedimiento de upgrade.
- Las reglas propias se tocan como cambio sensible: respaldo previo y
  validacion del conjunto antes de reiniciar el servidor, porque una regla mal
  escrita puede impedir que arranque (ver Alerts That Matter, caso 02).

## Validacion

- Una falla de backup genera el evento, el SIEM lo ingiere y la severidad es la
  esperada.
- El evento queda consultable despues, con dominio y error.

## Resultado

| Antes | Despues |
|---|---|
| Estado del backup solo en archivos locales | Evento estructurado en el SIEM |
| Alerta inmediata y nada mas | Alerta al telefono + evidencia consultable |
| Toda falla igual | Severidad segun el dominio afectado |

## Leccion

**La alerta es para actuar; la evidencia es para probar.** Son dos cosas y
necesitan dos lugares.
