# Informe final de Nubeduca (DOC-02)

Informe en LaTeX para compilar en Overleaf. Lo consolida **Mariano (Dev 2)**; cada dev escribe su parte.

Los textos en rojo (`\pendiente{...}`) son instrucciones de redacción: se borran cuando la parte está completa. Los recuadros "Evidencia pendiente" se reemplazan solos cuando existe la imagen en `latex/imagenes/`.

## Estructura

```text
nudeduca-informe/
├── README.md                         ← este archivo
└── latex/
    ├── main.tex                      ← documento principal (solo lo toca Mariano)
    ├── portada.tex                   ← falta el nombre del profesor
    ├── imagenes/                     ← logo, diagrama y capturas (ver imagenes/README.md)
    └── secciones/
        ├── 01-introduccion.tex
        ├── 02-arquitectura.tex
        ├── 03-microservicios.tex     ← tabla general + convenciones de datos
        ├── microservicios/           ← un archivo por microservicio
        │   ├── ms-usuarios.tex
        │   ├── ms-cursos.tex
        │   ├── ms-contenidos.tex
        │   ├── ms-evaluaciones.tex
        │   ├── ms-calificaciones.tex
        │   └── ms-notificaciones.tex
        ├── 04-eventos.tex
        ├── 05-seguridad-multitenant.tex
        ├── 06-frontend.tex
        ├── 07-despliegue-observabilidad.tex
        ├── 08-pruebas-rendimiento.tex
        ├── 09-limitaciones-learner-lab.tex
        ├── 10-repositorios.tex
        ├── 11-conclusiones.tex
        └── anexo-trazabilidad.tex    ← requisito del enunciado → sección
```

## Qué le toca a cada uno

| Dev | Archivos | Qué escribir |
|---|---|---|
| **Dev 1 · Fabricio** (@cruz-eng) | `07-despliegue-observabilidad.tex` (partes marcadas) · `05-seguridad-multitenant.tex` (captura del login real) | VM `nubeduca-despliegue`, cuenta de integración (D2), resultado de `desplegar.sh staging`, si se activó la protección de `main` y una captura del login con Cognito en producción |
| **Dev 2 · Mariano** (@MarianoUtec) | `01`, `04` (bus y outbox), `05`, `07` (CI y dashboard), `08` (PERF-01), `09`, `10`, `11`, anexo | **Ya redactado** con lo verificado; falta pegar capturas y consolidar |
| **Dev 3 · Benjamín** (@btoroled) | `02-arquitectura.tex` · `04-eventos.tex` (tabla de los 16 eventos) · `microservicios/ms-usuarios.tex` y `ms-cursos.tex` (con Edinson) | Revisar la arquitectura, exportar el diagrama, catálogo de eventos y tus microservicios |
| **Dev 4 · Edinson** (@Edinson695) | `microservicios/ms-evaluaciones.tex` · `ms-calificaciones.tex` (con Benjamín) · `ms-usuarios.tex` y `ms-cursos.tex` (con Benjamín) | Tus microservicios |
| **Dev 5 · Alexander** (@AIexander-lx) | `microservicios/ms-contenidos.tex` · auditoría (DAT-01) en `ms-calificaciones.tex` · diagrama en `imagenes/arquitectura.png` | Archivos en S3 con antivirus, módulos y auditoría de notas |
| **Dev 6 · Guillermo** (@Guillermo-Heredia) | `06-frontend.tex` · `microservicios/ms-notificaciones.tex` | Frontend en Amplify y notificaciones por correo |
| **Todos** | `08-pruebas-rendimiento.tex` (QA-01 y número de pruebas) · `11-conclusiones.tex` (una lección cada uno) | |

## Checklist de la subsección de cada microservicio

Cada archivo de `secciones/microservicios/` ya trae los eventos que publica y consume. Falta:

1. **Descripción** (2–3 párrafos): qué resuelve, decisiones de diseño y qué roles lo usan.
2. **Endpoints**: tabla con método, ruta, roles y evento que emite (sale del OpenAPI en `nubeduca-contratos/apis/`).
3. **Estructura JSON de la tabla** (lo pide el enunciado): un ítem real por cada tipo de `sk`. Para sacarlo:
   ```bash
   aws dynamodb scan --table-name dev-t_<nombre> --max-items 3
   ```
4. **Datos de prueba**: evidencia de los 30.000 registros (salida de `poblar_datos.py` o un conteo por tenant).
5. **Una captura** de una prueba funcional, guardada como `imagenes/ms-<nombre>-evidencia.png`.

Plantilla de tabla de endpoints:

```tex
\begin{table}[htbp]
\centering\small
\begin{tabularx}{\textwidth}{llXl}
\toprule
Método & Ruta & Roles & Emite \\
\midrule
GET  & \texttt{/courses} & TEACHER, ADMIN & --- \\
POST & \texttt{/courses} & TEACHER & \texttt{course.created} \\
\bottomrule
\end{tabularx}
\caption{Endpoints de ms-cursos.}
\end{table}
```

## Cómo trabajar

1. Rama `docs/DOC-02-<tu-parte>` en el repo, editas **solo tus archivos** y abres un PR hacia `main`.
2. No toques `main.tex` ni `portada.tex` (si necesitas un paquete de LaTeX, avísale a Mariano).
3. Las capturas van en `latex/imagenes/` con nombres sin espacios. Nunca incluyas contraseñas, tokens ni claves de AWS.
4. Escapa los caracteres especiales en el texto: `\_` para `_`, `\#` para `#`, `\%` para `%`. Las URL van con `\url{...}`.
5. Dentro de `lstlisting` (bloques JSON) no hace falta escapar nada.
6. Las figuras y tablas **se numeran solas**. Si en el texto mencionas una, no escribas "Figura 3": usa `Figura~\ref{fig:etiqueta}` (la etiqueta es el tercer argumento de `\evidencia{...}{...}{fig:etiqueta}`) o `Tabla~\ref{tab:etiqueta}` (la del `\label{...}` de la tabla). Así, si alguien agrega o quita una imagen, todo se renumera al compilar.

## Compilar en Overleaf

1. Comprime **el contenido de la carpeta `latex/`** como ZIP (`main.tex` debe quedar en la raíz del ZIP).
2. En Overleaf: **New Project → Upload Project** y sube el ZIP.
3. Comprueba que el documento principal es `main.tex` y el compilador **pdfLaTeX** (Menú → Settings).
4. Recompila y revisa la portada, el índice, las tablas y las capturas.

GitHub es la fuente de verdad: si corriges algo en Overleaf, devuélvelo al repo. La sincronización automática de Overleaf con GitHub es de pago.

En local también se puede compilar con `latexmk -pdf main.tex` (dentro de `latex/`).

## Antes de entregar

- [ ] Sin textos en rojo (`\pendiente`) ni recuadros de "Evidencia pendiente".
- [ ] Nombre del profesor en `portada.tex`.
- [ ] Estructura JSON de las 6 tablas con ítems reales.
- [ ] Enlaces de `10-repositorios.tex` abiertos en incógnito.
- [ ] Anexo de trazabilidad revisado contra el enunciado.
