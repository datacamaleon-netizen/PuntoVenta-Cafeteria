# POS Cafetería - Sistema de Punto de Venta Modular

Este proyecto es un sistema de Punto de Venta (POS) personalizado, diseñado específicamente para satisfacer las necesidades de pequeñas y medianas cafeterías. El objetivo principal es ofrecer una solución interoperable, accesible desde diversos dispositivos (tablets o computadoras) y con un costo de licenciamiento cero, eliminando la necesidad de adquirir software corporativo costoso.

---

## 📄 Registro de Decisión Arquitectónica (ADR-01)

### Contexto y Problema
El sistema debe gestionar ventas, usuarios y catálogos esenciales para PyMEs del sector gastronómico (cafeterías), donde la inversión en licencias de software corporativo no es viable. El desarrollo se realiza bajo las siguientes restricciones de entorno:
* **Equipo:** Desarrollador individual.
* **Tiempo:** Un cuatrimestre académico.
* **Conocimiento:** Proceso de aprendizaje del lenguaje C# y el ecosistema .NET, requiriendo alta disponibilidad de documentación.
* **Necesidad:** Interoperabilidad en múltiples dispositivos (tablets/PCs) sin costos operativos excesivos.
* **Infraestructura de Despliegue:** En esta fase inicial, el sistema correrá en **localhost (servidor de desarrollo local)** utilizando las capacidades de hospedaje integradas de ASP.NET Core y una instancia local de SQL Server. Esta decisión de infraestructura es totalmente coherente con el estilo de Monolito Modular elegido, ya que permite probar toda la solución en un único entorno controlado sin agregar la complejidad de red de una nube distribuida antes de tiempo.

### Decisión Adoptada
Se ha decidido adoptar un estilo de **Arquitectura en Capas (N-Tier)** implementado bajo un **Monolito Modular**, utilizando la plataforma **.NET 8**, el lenguaje **C#** y **Entity Framework Core** para la persistencia de datos en **SQL Server**.

#### ¿Por qué? (Justificación)
1. **Separación de Incumbencias:** Aislar las reglas del negocio de los detalles técnicos y la base de datos permite realizar cambios modulares sin propagar errores en todo el sistema.
2. **Productividad con .NET 8 y EF Core:** El mapeo de objetos directo a la base de datos reduce drásticamente la escritura manual de consultas SQL, acelerando el desarrollo individual.
3. **Seguridad y Tipado Fuerte:** La detección de errores en tiempo de compilación mitiga fallos críticos en producción mientras se domina la sintaxis del lenguaje.
4. **Costo Cero de Licencias:** El ecosistema de .NET es Open Source y multiplataforma, cumpliendo el requerimiento de accesibilidad económica para las PyMEs locales.

---

## 🛠️ Stack Tecnológico

- **Lenguaje:** C# (.NET 8)
- **Framework Web:** ASP.NET Core (MVC / Blazor)
- **Persistencia de Datos:** Entity Framework Core (ORM)
- **Base de Datos:** SQL Server

---

## 📐 Estructura del Sistema (Capas)

1. **Capa de Presentación (ASP.NET Core):** Interfaz de usuario responsiva y accesible mediante navegadores web desde tablets o computadoras.
2. **Capa de Aplicación y Dominio:** Contiene las reglas del negocio, validaciones esenciales y los casos de uso principales (procesar venta, registrar producto).
3. **Capa de Infraestructura (EF Core):** Implementación de la persistencia de datos y acceso al ORM a través de interfaces y repositorios.
4. **Capa de Datos:** Base de datos centralizada administrada en SQL Server.

### 🔄 Alternativas Consideradas y Descartadas

| Alternativa | Razón de Descarte |
| :--- | :--- |
| **Microservicios** | Introduciría una complejidad operativa y de red excesiva, imposible de gestionar de manera eficiente por un solo desarrollador en el lapso de un cuatrimestre académico. |
| **Arquitectura de "Caja de Arena" (Sin Capas)** | Crearía un acoplamiento crítico (*"Gran bola de lodo"*), volviendo el código frágil, inmanejable y sumamente difícil de extender o corregir a mediano plazo. |
| **Desktop Nativo (WinForms)** | Limitaría el ecosistema exclusivamente a equipos con sistema operativo Windows, rompiendo el requerimiento de interoperabilidad flexible para tablets. |

---

### ⚖️ Consecuencias y Trade-offs

#### ✅ Lo que gano (Beneficios)
* **Consecuencia Técnica:** Alta testabilidad y mantenibilidad del código. El uso de inyección de dependencias e interfaces permite aislar componentes y realizar pruebas unitarias robustas en la lógica del negocio.
* **Consecuencia sobre el Proceso:** Curva de trabajo estructurada y clara. Las responsabilidades de cada capa agilizan el flujo de desarrollo al saber exactamente dónde incorporar cada nueva funcionalidad.

#### ⚠️ Lo que sacrifico / asumo (Riesgos)
* **Limitación Técnica:** Existe un leve sobrecosto en el rendimiento general por el flujo de datos intercapas y el mapeo continuo de objetos mediante DTOs (*Data Transfer Objects*), en comparación con llamadas directas y monolíticas a la base de datos.
* **Deuda Técnica o Riesgo:** Escalabilidad horizontal limitada. Si el sistema llegara a escalar a miles de sucursales simultáneas, la base de datos centralizada del monolito podría volverse un cuello de botella, requiriendo una futura migración a una base de datos distribuida o a servicios independientes.

---

## 📊 Diagrama de Arquitectura (Modelo C4 - Nivel 2: Contenedores)

```mermaid
graph TB
    %% Definición de Estilos C4
    classDef person fill:#08427b,stroke:#073b6f,color:#ffffff,font-weight:bold;
    classDef container fill:#1168bd,stroke:#0b4e8f,color:#ffffff;
    classDef external fill:#7f7f7f,stroke:#595959,color:#ffffff;
    classDef database fill:#1168bd,stroke:#0b4e8f,color:#ffffff,shape:cylinder;

    Usuario[/"👤 Usuario de la Cafetería<br/>(Cajero / Administrador)"/]:::person

    subgraph Sistema [Monolito Modular - Sistema POS]
        
        subgraph Pres [Capa de Presentación]
            UI["💻 Aplicación Web<br/>[ASP.NET Core 8]<br/>Interfaz responsiva."]:::container
        end

        subgraph Logica [Capa de Aplicación y Dominio]
            Services["⚙️ Servicios y Casos de Uso<br/>[C# Core Library]<br/>Reglas de negocio."]:::container
        end

        subgraph Infra [Capa de Infraestructura]
            EF["🛠️ Persistencia<br/>[Entity Framework Core]<br/>Acceso a datos e interfaces."]:::container
        end
        
        subgraph Datos [Capa de Datos]
            DB[("🗄️ Base de Datos<br/>[SQL Server]<br/>Ventas, productos y usuarios.")]:::database
        end

    end

    Usuario --> UI
    UI --> Services
    Services -.-> EF
    EF --> DB

    style Sistema fill:#f9f9f9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5;
