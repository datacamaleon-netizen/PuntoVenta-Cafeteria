# POS Cafetería - Sistema de Punto de Venta Modular

Este proyecto es un sistema de Punto de Venta (POS) personalizado, diseñado específicamente para satisfacer las necesidades de pequeñas y medianas cafeterías. El objetivo principal es ofrecer una solución interoperable, accesible desde diversos dispositivos (tablets o computadoras) y con un costo de licenciamiento cero, eliminando la necesidad de adquirir software corporativo costoso.

## 🛠️ Stack Tecnológico

- **Lenguaje:** C# (.NET 8)
- **Framework Web:** ASP.NET Core (MVC / Blazor)
- **Persistencia de Datos:** Entity Framework Core
- **Base de Datos:** SQL Server

---

## 📐 Diseño Arquitectónico

El sistema adopta un enfoque de **Arquitectura en Capas (N-Tier)** implementado bajo un **Monolito Modular**. Esta decisión estratégica garantiza la separación de incumbencias, facilitando la modificabilidad, la mantenibilidad y la testabilidad del software sin comprometer las reglas del negocio.

### Estructura de Capas:
1. **Capa de Presentación (ASP.NET Core):** Interfaz de usuario responsiva y accesible vía web/navegador.
2. **Capa de Aplicación y Dominio:** Contiene los casos de uso, servicios y reglas de negocio esenciales de la cafetería (gestión de ventas, validaciones).
3. **Capa de Infraestructura (EF Core):** Implementación de la persistencia de datos y acceso al ORM.
4. **Capa de Datos:** Base de datos centralizada en SQL Server.

---

## 📊 Diagrama de Arquitectura (Modelo C4 - Nivel 2: Contenedores)

A continuación, se presenta cómo se estructuran los contenedores del sistema y el flujo de comunicación entre ellos:

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
