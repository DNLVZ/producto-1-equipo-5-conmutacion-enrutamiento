# Guía de pruebas

Realizar cada comando cuando aparezca el prompt. Las consolas VPCS pueden perder los primeros caracteres si se escriben varios comandos sin esperar.

## Estado inicial

En SWM1 y SWM2: `show standby brief`, `show etherchannel summary`, `show spanning-tree vlan 10`, `show vtp status`, `show vlan brief`, `show ip ospf neighbor`.

Esperado: SWM1 activo con prioridad 110; SWM2 standby con prioridad 100. Po1(SU) con Et0/1(P), Et0/2(P), Et0/3(P).

En R1: `show ip ospf neighbor`, `show ip route ospf`, `show ip route 0.0.0.0`, `show ipv6 route`. Deben aparecer R2, SWM1 y SWM2 como vecinos FULL y rutas O IA.

## Conectividad y ACL

En EST1: `ping 172.24.11.10 -c 3`, `ping 2001:25:24:5::10 -c 3`. Esperado: respuestas del servidor. `ping 172.24.10.130 -c 3` y `ping 172.24.9.10 -c 3`: bloqueados por política. Confirmar contadores con `show access-lists ESTUDIANTES-IN` en el gateway activo.

En GES1, DOC1, FIN1 y ACA1: ping a 172.24.11.10 y 2001:25:24:5::10. Registrar resultados individuales. Esta sesión repitió Estudiantes y Gestión; las restantes PCs constaban en las pruebas anteriores.

## Un miembro EtherChannel

En SWM1: `configure terminal`, `interface ethernet 0/1`, `shutdown`, `end`. Comprobar Po1(SU), 0/2(P) y 0/3(P); repetir ping al servidor.

Restauración obligatoria: `configure terminal`, `interface ethernet 0/1`, `no shutdown`, `end`. Esperar la convergencia y comprobar los tres miembros P antes de pasar a la siguiente prueba.

## HSRP

En SWM1: `configure terminal`, `interface ethernet 0/0`, `shutdown`, `end`. Esperar que SWM2 sea Active en todos los grupos y repetir ping desde EST1. Registrar pérdida y recuperación; no afirmar cero pérdida ni un tiempo no medido.

Restauración obligatoria: `configure terminal`, `interface ethernet 0/0`, `no shutdown`, `end`, `write memory`. Verificar SWM1 Active, SWM2 Standby y vecinos OSPF FULL.

## Demostraciones adicionales

Probar la caída completa de SWM1 desde el control del nodo, restaurarlo y verificar la recuperación. Para demostrar una transición desde OSPF de área única a multiarea, usar una copia de laboratorio y conservar el original; aún no se ha documentado esta transición. Las pruebas de ACL estándar deben registrar contadores y usar un origen autorizado de prueba, restaurándolo después.
