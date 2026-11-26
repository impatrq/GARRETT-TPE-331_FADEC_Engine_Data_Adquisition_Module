# 🛩️ GARRETT TPE-331 — FADEC Engine Data Acquisition Module

Este repositorio contiene el diseño electrónico (esquemáticos, layout de PCB, lista de materiales y documentación técnica) del **Módulo de Adquisición de Datos de Motor (Engine Data Acquisition Module)** orientado al sistema **FADEC** (*Full Authority Digital Engine Control*) y monitoreo de parámetros críticos de la turbina turbohélice **Garrett TPE-331**.

---

## 📐 Descripción del Proyecto

El objetivo principal es diseñar y fabricar una placa de circuito impreso (**PCB**) de grado aeronáutico/experimental capaz de acondicionar, digitalizar y transmitir en tiempo real las variables termodinámicas, mecánicas y de combustible del motor turbohélice **Garrett TPE-331** hacia la unidad central de control (**FADEC** / ECU) y sistemas de telemetría o registro de datos en cabina.

### Funcionalidades Principales:
* **Acondicionamiento de Sensores de Turbina:** Etapas analógicas dedicadas para termocuplas de alta temperatura, sensores de velocidad magnéticos (variable reluctance), transductores de presión y torque.
* **Adquisición Determinística de Alta Precisión:** Conversión analógica-digital multicanal con filtrado anti-aliasing y aislamiento galvánico/protección contra transitorios electromagnéticos (EMI/EMC).
* **Diagnóstico y Detección de Fallas (BITE):** Monitoreo de circuito abierto, cortocircuito y fuera de rango en sensores críticos de vuelo.
* **Comunicaciones Aeronáuticas:** Interfaz robusta para envío de tramas de datos hacia el controlador FADEC (ARINC 429 / CAN Bus / RS-422).
* **Alimentación Aeronáutica (28 VDC):** Etapa de regulación con protección contra inversión de polaridad, picos de carga (*load dump*) y transitorios según normativa MIL-STD-704 / DO-160.

---

## ⚙️ Arquitectura de Hardware y Sensores Soportados (TPE-331)

*(Ajustar los canales, rangos y modelos específicos según el diseño final del esquemático)*

| Variable / Parámetro | Sensor Típico (Garrett TPE-331) | Acondicionamiento / Interfaz |
| :--- | :--- | :--- |
| **EGT / ITT (Temp. Gases de Escape / Interturbina)** | Arnés de Termocuplas Tipo K (Chromel-Alumel) | Amplificador con compensación de junta fría (CJC) y filtro EMI |
| **RPM (% N1 / N2 - Velocidad de Rotor)** | Captor magnético de reluctancia variable (Monopole) | Acondicionador de cruce por cero / Schmitt Trigger (ej. MAX9924/26) |
| **Torque (Par Motor)** | Transductor de presión diferencial de aceite / sensor de par | Amplificador de instrumentación de baja deriva + ADC de alta resolución |
| **Presión y Temp. de Aceite** | Transductores resistivos / 4-20 mA / RTD | Puente de Wheatstone / Fuente de corriente + Buffer operacional |
| **P2 / T2 (Presión y Temp. de Entrada)** | Sensor piezorresistivo absoluto y bulbo RTD / Termistor | Acondicionamiento diferencial y linealización |
| **P3 (Presión de Descarga del Compresor - CDP)** | Transductor de presión absoluta de alta presión | Filtro pasa-bajos anti-aliasing + Protección contra sobrepresión |
| **Fuel Flow (Flujo de Combustible)** | Caudalímetro de turbina (frecuencia) | Entrada digital aislada por captura de pulsos |
| **Posición Power Lever / Condition Lever (PLA / CLA)** | RVDT / Potenciómetros redundantes de doble pista | Excitación AC / Lectura diferencial redundante |
| **Tensión de Bus Aeronáutico** | Bus principal 28 VDC | Divisor de tensión aislado + Supresor TVS |

---

## 📂 Estructura del Repositorio

```text
GARRETT-TPE-331_FADEC_Engine_Data_Adquisition_Module/
├── hardware/               # Archivos fuente CAD (KiCad / Altium) - Esquemáticos y PCB
├── fabrication/            # Archivos Gerber, Drill, Pick & Place y planos de ensamblaje
├── bom/                    # Lista de materiales (Bill of Materials) y hojas de datos
├── firmware/               # Código embebido de adquisición, calibración y BITE (si aplica)
├── docs/                   # Diagramas en bloques, pinout de conectores y notas de diseño
└── README.md               # Documentación principal del proyecto
```

---

## 🛠️ Normas de Diseño y Fabricación

* **Estándares de Diseño PCB:** IPC-2221 (Diseño genérico de tarjetas impresas) e IPC-7351 (Huellas SMD).
* **Integridad de Señal y EMC:** Separación estricta de planos de masa analógica (`AGND`), digital (`DGND`) y chasis, blindaje en entradas de termocuplas y protección ESD/TVS en todos los conectores externos.
* **Trazabilidad y Nomenclatura:** Identificación clara en serigrafía (*Silkscreen*) para conectores, polaridad y puntos de prueba (`TP`).
* **Archivos de Salida:** Gerbers en formato RS-274X y archivos de taladrado Excellon.

---

## 🌿 Estructura de Ramas

Este repositorio sigue una convención de dos ramas principales:

| Rama | Propósito |
| :--- | :--- |
| `main` | Diseño hardware validado y listo para fabricación. Nunca se pushea directo. Solo acepta merges vía PR aprobada por un docente. |
| `develop` | Rama de integración de cambios en esquemáticos, layout y BOM. Cualquier becario puede hacer merge vía PR sin requerir aprobación. |

Las ramas de trabajo se crean a partir de `develop` con nombres descriptivos:
* `feature/nombre-del-bloque` *(ej. `feature/acondicionamiento-egt`)*
* `fix/descripcion-del-error` *(ej. `fix/huella-conector-ms`)*
* `docs/lo-que-se-documenta` *(ej. `docs/pinout-arnes-motor`)*

---

## 🔄 Flujo de Trabajo

`[rama feature]` → PR → `[develop]` → PR (aprobación docente) → `[main]`

1. **Creá tu rama desde `develop`:**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/mi-bloque-pcb
   ```
2. **Trabajá en tu rama y commiteá con mensajes descriptivos:**
   ```bash
   git commit -m "feat: agrega acondicionamiento para sensores EGT y CJC"
   ```
3. **Abrí una *Pull Request* hacia `develop` cuando terminés.**
4. **Una vez integrado en `develop` y validado (DRC/ERC sin errores), un docente abre la PR hacia `main`.**

> ⚠️ **Nunca pushear directamente a `main` ni a `develop`.** Siempre trabajar desde ramas propias y abrir PR.

---

## 📋 Convención de Commits

Usamos una convención simple basada en prefijos:

| Prefijo | Cuándo usarlo |
| :--- | :--- |
| `feat:` | Nuevo bloque circuital, sensor o componente agregado al diseño |
| `fix:` | Corrección de errores en esquemático, ruteo o huellas (footprints) |
| `docs:` | Cambios en documentación, pinouts o README |
| `refactor:` | Reorganización de layout o esquemático sin cambiar funcionalidad |
| `chore:` | Actualización de BOM, librerías o archivos de fabricación |

---

## 👥 Equipo

| Rol | Responsabilidades en GitHub |
| :--- | :--- |
| **Docentes** | Aprueban PRs hacia `main`, administran la organización y validan diseños para fabricación |
| **Becarios** | Crean ramas, diseñan esquemáticos/PCB, abren PRs hacia `develop` y participan en revisiones |

---

## 🔗 Recursos

* [Organización en GitHub (impatrq)](https://github.com/impatrq)
* [Repositorio del proyecto](https://github.com/impatrq/GARRETT-TPE-331_FADEC_Engine_Data_Adquisition_Module)
