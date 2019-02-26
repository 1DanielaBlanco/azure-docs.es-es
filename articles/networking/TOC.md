# Información general
## [Información acerca de redes de Azure](networking-overview.md)
## Arquitectura
### [Centros de datos virtuales](/azure/architecture/vdc/networking-virtual-datacenter)
### [Enrutamiento asimétrico con varias rutas de acceso de red](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Protección de diseños de redes](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topología en estrella tipo hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Procedimientos recomendados de seguridad de red](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Aplicaciones virtuales de red de alta disponibilidad](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Combinación de métodos de equilibrio de carga](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Recuperación ante desastres mediante Azure DNS y Traffic Manager](disaster-recovery-dns-traffic-manager.md)
## Planeamiento y diseño
### [Redes virtuales](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividad entre implementaciones locales: VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividad entre implementaciones locales: conexión privada dedicada](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)
### Interoperabilidad de conectividad de back-end
#### [Preparación y configuración de la prueba](connectivty-interoperability-preface.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Configuración de la prueba](connectivty-interoperability-configuration.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Análisis del plano de control](connectivty-interoperability-control-plane.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Análisis del plano de datos](connectivty-interoperability-data-plane.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Conceptos
### [Redes virtuales](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Equilibrio de carga de red](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Equilibrio de carga de la aplicación](../application-gateway/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribución de tráfico basado en DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conexión local: VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conexión local: Dedicada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Introducción
## [Creación de su primera red virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Procedimientos
## Conectividad de Internet
### [Servidores públicos de equilibrio de carga de red](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores públicos de equilibrio de carga de aplicación](../application-gateway/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Protección de aplicaciones web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribución de tráfico entre ubicaciones](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividad interna
### [Servidores privados de equilibrio de carga de red](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores privados de equilibrio de carga de aplicación](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conexión de redes virtuales (misma ubicación)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conexión de redes virtuales (diferente ubicación)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividad entre implementaciones locales
### [Creación de una conexión VPN de sitio a sitio (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Creación de una conexión VPN P2S (SSTP con certificados)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Creación de una conexión privada dedicada (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Administración
### [Introducción a la supervisión de red](network-monitoring-overview.md)
### [Comprobación del uso de los recursos en comparación con los límites de Azure](check-usage-against-limits.md)
### [Visualización de la topología de red](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Scripts de ejemplo
### [CLI de Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Tutoriales
### [Equilibrio de carga de máquinas virtuales](../virtual-machines/linux/tutorial-load-balancer.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conexión de un equipo a una red virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Referencia
## [CLI de Azure](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Recursos
## [Creación de plantillas](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Plantillas de la comunidad](https://azure.microsoft.com/resources/templates/)
## [Blog de redes](https://azure.microsoft.com/blog/topics/networking)
## [Precios](https://azure.microsoft.com/pricing)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilidad regional](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

