# Producto 1 · Implementación de una red empresarial

**Equipo 5 · Conmutación y Enrutamiento · ITLA**  
**Docente:** Onel Luis Pelegrino

Este repositorio reúne la topología de PNETLab, las configuraciones individuales de los 16 nodos y el informe ejecutivo revisado por el equipo. El escenario integra dos sedes, segmentación por departamentos, redundancia de gateway y enlaces, enrutamiento IPv4 multiárea y políticas de acceso.

## Integrantes

| Integrante | Matrícula |
| --- | --- |
| Jeison Roniel Baez Castillo | 2025-0063 |
| Taer Kalany Calderon Espaillat | 2024-0969 |
| Jesús Daniel Vásquez Bonilla | 2025-1338 |
| Ian Daliel Ventura Francisco | 2025-0672 |

## Archivos principales

- [Topología importable en PNETLab](topologia/Equipo5-PNETLab.zip).
- [Archivo UNL de la topología](topologia/PRODUCTO%201%20EQUIPO%205.unl).
- [Configuraciones individuales en TXT](configuraciones/).
- [Preparación de VLANs y VTP](preparacion-VLAN-VTP/).
- [Informe ejecutivo revisado en PDF](documentacion/Informe-ejecutivo-Equipo5.pdf).
- [Procedimiento de validación y restauración](PRUEBAS.md).

Los TXT se extrajeron del campo `config_data` de la topología final descargada. Conservan los nombres de los equipos y sus configuraciones. Los archivos de preparación complementan los datos de VLAN/VTP que pueden almacenarse fuera de running-config.

## Diseño

- IPv4 asignado: `172.24.0.0/15`.
- IPv6 asignado: `2001:25:24::/48`.
- Sede principal: SWM1 y SWM2 como distribución; SWAC1, SWAC2 y SWAC3 como acceso.
- Sucursal: R2, SWA13 y SW-DC; departamentos Académicos, Finanzas y Datacenter.
- VLANs de la sede: 10 Estudiantes, 20 Docentes, 30 Gestión, 40 Voz y 50 Blackhole.
- VLANs de sucursal: 60 Finanzas, 70 Académicos, 80 Datacenter y 90 Blackhole.
- LACP: Port-channel1 con Ethernet0/1, 0/2 y 0/3 entre SWM1 y SWM2.
- Rapid PVST, HSRP v1 con seguimiento de enlace y OSPFv2 proceso 5 en áreas 0, 10 y 20.
- IPv6 mediante rutas estáticas, con rutas flotantes en R1 hacia la sede.
- ACLs para aislar Estudiantes de Finanzas y Gestión y controlar orígenes de administración y de salida.

## Importación y puesta en marcha

1. Descargar e importar `topologia/Equipo5-PNETLab.zip` en PNETLab. Se conservó sin modificar el ZIP exportado desde el laboratorio.
2. Disponer de las imágenes IOL y VPCS que pide la topología. Este repositorio no distribuye imágenes de Cisco ni otros binarios de los dispositivos.
3. Revisar **Startup Configs**. La exportación conserva algunos nodos Cisco con el uso de configuración exportada desactivado. En una instalación nueva, seleccionar sus configuraciones exportadas o aplicar el TXT correspondiente desde la consola.
4. Reconstruir VLANs y VTP con `preparacion-VLAN-VTP/`: preparar primero SWM1, SWA13 y SW-DC, y después los clientes. Verificar `show vlan brief` y `show vtp status` antes de probar tráfico. Iniciar el servidor VTP antes de los clientes.
5. Los archivos Cisco son configuraciones completas: entrar en modo de configuración global, omitir las cabeceras informativas y terminar con `end` y `write memory`. En VPCS ejecutar los comandos individualmente, esperar el prompt y finalizar con `save`.
6. Seguir `PRUEBAS.md`. Restaurar cada enlace al terminar la prueba y comprobar que SWM1 quede activo y SWM2 en reserva.

La importación en una instalación limpia requiere esta revisión; no se presenta como una restauración independiente ya probada.

## Resultados documentados

En la revisión del 20 de octubre de 2026, Estudiantes y Gestión obtuvieron tres respuestas del servidor en IPv4 y en IPv6. La ACL rechazó los intentos de Estudiantes hacia Finanzas. Port-channel1 continuó operativo con un miembro apagado. HSRP recuperó el servicio mediante SWM2 y se restauró el principal al terminar. R1 mostró tres vecinos OSPF FULL y rutas entre áreas.

Durante la convergencia hubo pérdida temporal. El ISP es simulado y no representa acceso a Internet real. La prueba de caída completa del switch y la transición independiente de OSPF de área única a multiárea no están documentadas como terminadas. Tampoco se realizaron pruebas de carga ni llamadas de voz. Los alcances y límites se explican en el informe.
