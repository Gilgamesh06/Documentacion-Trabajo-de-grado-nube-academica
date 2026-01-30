# Introduccion

1. **FastAPI tiene una *Arquitectura oficial***

    * **FastAPI No impone una arquitectura rígida,** a diferencia de Spring Boot.
    * Lo que si ofrece es:

        * Un **framework web minimalista**
        * Basado en **ASGI**
        * Muy alineado con **principios SOLID**
        * Pensado para **arquitecturas limpias y desacopladas**

    * FastAPI te da **herramientas**, no una estructura forzada.
    * El *estadar* real surge de **buenas practicas compartidas por la comunidad**

2. **Arquitectura típica (Estadar de facto en FastAPI)**

    * La arquitectura más usada es una **arquitectura en capas**, muy parecida a lo que ya conoces en Spring Boot

        ```bash
        app/
        ├── main.py
        ├── api/
        │   ├── v1/
        │   │   ├── endpoints/
        │   │   │   ├── users.py
        │   │   │   └── orders.py
        │   │   └── router.py
        ├── core/
        │   ├── config.py
        │   └── security.py
        ├── models/
        │   ├── orm/
        │   │   └── user.py
        │   └── schemas/
        │       └── user.py
        ├── services/
        │   └── user_service.py
        ├── repositories/
        │   └── user_repository.py
        └── db/
            ├── session.py
            └── base.py
        ```
        * **Api/ Endpoints:** Recibe request HTTP
        * **Schemas (DTOs):** Validación y serialización
        * **Services:** Lógica de negocio
        * **Repositories:** Acceso a datos
        * **Models ORM:** Representación de BD
        * **Core:** Configuración y seguridad
        * **DB:** Conexión, sesiones

    * Esto es **Clean Architecture / Layered Architecture aplicada a FastAPI**

3. **Porque FastAPI favorece esta arquitectura?**

    * Porque FastAPI:

        * Usa **Dependency Injection**
        * Usa **tipado fuerte**
        * No mezcla lógica de negocio con HTTP
        * Facilita pruebas unitarias
        * Escala muy bien a microservicios

    * FastAPI **premia el desacoplamiento**

4. **Patrones de diseño que FastAPI usa (o incentiva)**

    * FastAPI **No implementa explícitamente patrones,** pero **los habilita naturalmente**

    1. **Dependency Injection (Patron Clave)**

        * Es el patrón mas importante en FastAPI

            ```python
            def get_db():
                db = SessionLocal()
                try:
                    yield db
                finally:
                    db.close()
            @app.get("/users")
            def get_users(db: Session = Depends(get_db)):
                ...
            ```
            * Bajo acoplamiento
            * Facil testing
            * Reemplazo de dependencias (mock)
        
        * **Equvalente conceptual a** `@Autowired` **en Spring**

    2. **Repository Pattern**

        * Separar **lógica de acceso a datos**

            ```python
            class UserRepository:
                def get_by_id(self, db, user_id: int):
                    return db.query(User).filter(User.id == user_id).first()
            ```
            * Cambiar ORM sin tocar servicios
            * Test más simples
            * Lógica de DB centralizada

    3. **Service Layer Pattern**

        * La **Logica de negocio No va en el endpoint**

            ```python
            class UserService:
                def __init__(self, repo: UserRepository):
                    self.repo = repo
                
                def create_user(self, user_data):
                    ...
            ```
            * Endpoints delgados
            * Reutilización
            * Código mantenible

    4. **DTO/ Schema Pattern (PyDantic)**

        * FastAPI lo usa de forma nativa:

            ```python
            class UserCreate(BaseModel):
                email: EmailStr
                password: str
            ````
            * Validación automática
            * Seguridad (no expones campos internos)
            * Documentacion OpenAPI automatica
            
        * Equvalente a **DTOs en Spring**

    5. **Factroy Pattern (indirecto)**

        * Muy común para:

            * Crear sesiones de DB
            * Crear clientes externos
            * Crear Servicios

            ```python
            def get_user_service():
                return UserService(UserRepository())
            ```
            * FastAPI lo combina con **Dependency Injection**

    6. **Singleton (Controlado)**

        * FastAPI evita singletons *clasicos*, pero:

            * Configuración
            * Pool de conexiones
            * Cliente HTTP

            ```python
            settings = Settings()
            ```
            * Se usa **estado compartido controlado**, no singletons rigidos

    7. **MiddlewarePattern**

        * FastAPI implementa middleware ASGI

            ```python
            @app.middleware("http")
            async def log_requests(request, call_next)
                ...
            ```
            * Cross-cutting concerns
            * Logging
            * Auth
            * Metrics

    ## Relación con pricipios SOLID

    * FastAPI **está diseñado para que apliques SOLID facilmente:**

        | Principio | Cómo se aplica            |
        | --------- | ------------------------- |
        | S         | Endpoints delgados        |
        | O         | Nuevos routers sin romper |
        | L         | Interfaces implícitas     |
        | I         | Dependencias pequeñas     |
        | D         | `Depends()`               |
