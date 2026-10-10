# Local Cloud Project – Orquestador de Nube Privada (PUCP)

Sistema para desplegar *slices* (topologías de VMs y redes VLAN) sobre un clúster Linux con QEMU/KVM y Open vSwitch. Incluye los scripts de infraestructura, un orquestador en Python, un backend alternativo con autenticación y varios frontends.

## Estructura

| Carpeta / archivo | Qué es |
|---|---|
| `*.sh` | Scripts base que corren en los servidores: `headnode_init.sh`, `compute_init.sh`, `create_network_vlan.sh`, `create_vm.sh`, `delete_vm.sh`, `routing_networks.sh`, `internet_to_network.sh` y sus variantes `disable_*`. Cada uno documenta sus parámetros en la cabecera. |
| `orchestrator/` | Orquestador en Python (Flask). Modela Slice → Networks → VMs, driver para clúster Linux con OvS/QEMU, persistencia en archivos y API REST. Tiene su propio `README.md`, `QUICKSTART.md` y `ARCHITECTURE.md`. |
| `mi-proyecto/` | Variante con Docker Compose: Keycloak (auth), backend FastAPI que ejecuta los scripts por SSH y un portal estático servido con nginx. |
| `Spring Boot/` | Backend Java 17 + Spring Boot + JPA + MySQL con vistas Thymeleaf (login, dashboard, creación de slice). Esquema en `db_sql.sql`. |
| `Cloud-front/` | Frontend React (Create React App). |
| `Front_new_Cloud/` | Frontend nuevo basado en Ant Design Pro / UmiJS, con roles admin, profesor y usuario. |
| `DEPLOYMENT_GUIDE.md` | Guía paso a paso para desplegar en la infraestructura del laboratorio (Server1-2 compute, Server3 headnode, Server4 cliente). |
| `test_deployment.py` | Prueba rápida desde Server4: SSH, inicialización, slice de prueba y validación. |

## Arranque rápido

**Infraestructura** (en cada servidor, ver `DEPLOYMENT_GUIDE.md` para el detalle):

```bash
sudo ./headnode_init.sh ens4          # en el headnode
sudo ./compute_init.sh ens4           # en cada nodo de cómputo
sudo ./create_network_vlan.sh 100 192.168.0.0/24 yes 192.168.0.10,192.168.0.100
sudo ./create_vm.sh vm-vlan100 br-int 100 1
```

**Orquestador**:

```bash
cd orchestrator
pip install -r requirements.txt
python init_orchestrator.py
python api.py
```

**Stack con Keycloak**:

```bash
cd mi-proyecto
docker compose up --build
# Keycloak :8080, backend :8000, portal :80
```

**Frontend nuevo**:

```bash
cd Front_new_Cloud
npm install
npm start
```

## Notas

- `implementation-v1/` está vacía.
- El `Spring Boot` apunta a MySQL local (`pucp_cloud_orchestrator`, usuario root). Ajusta `application.properties` antes de correrlo.
