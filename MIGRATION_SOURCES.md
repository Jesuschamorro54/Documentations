# Documentación de Migración - Modelo Sources

## 📋 Resumen

Este documento describe los cambios en el modelo de datos de **Sources** (clases/recursos de cursos). La migración incluye actualizaciones de campos, deprecaciones y nuevos campos añadidos.

---

## 🔄 Cambios en el Modelo

### 1. **annex_file** - ACTUALIZACIÓN CRÍTICA

**Antes:**
```json
"annex_file": {
  "file_bucket": "",
  "file_ext": "",
  "file_name": "",
  "file_type": "",
  "file_url": ""
}
```

**Después:**
```json
"annex_file": [
  {
    "file_bucket": "",
    "file_ext": "",
    "file_name": "",
    "file_type": "",
    "file_url": ""
  }
]
```

**Cambio:** `annex_file` pasa de ser un **objeto** a un **array de objetos**.

---

### 2. **date** → **created_at** - RENOMBRADO

**Antes:**
```json
"date": "2025-03-11"
```

**Después:**
```json
"created_at": "2025-11-05T21:15:23.357205Z"
```

**Cambio:** El campo `date` se renombra a `created_at` para mayor claridad semántica.

---

### 3. **date_update** → **last_update** - RENOMBRADO

**Antes:**
```json
"date_update": "2025-11-07 20:39:34"
```

**Después:**
```json
"last_update": "2025-11-07 20:39:34"
```

**Cambio:** El campo `date_update` se renombra a `last_update` para consistencia.

---

### 4. **last_update_by** - NUEVO CAMPO

```json
"last_update_by": "425034"
```

**Cambio:** Nuevo campo que almacena el ID del usuario que realizó la última actualización.

---

### 5. **description** (nivel raíz) - DEPRECADO ⚠️

```json
"description": ""  // ❌ DEPRECADO
```

**Cambio:** El campo `description` a nivel raíz del source ya no se utilizará.

**Nota:** La descripción del contenido sigue existiendo dentro del array `content` para elementos de tipo `text`

**Impacto:**
- ✅ **Backend:** 
  - Puede remover validaciones de este campo
  - Considerar eliminación gradual de la columna en DB
- ✅ **Frontend:** 
  - Dejar de mostrar/editar este campo
  - Usar solo las descripciones dentro de `content`

---

### 6. **file_information** (nivel raíz) - DEPRECADO ⚠️

```json
"file_information": {  // ❌ DEPRECADO
  "file_duration": "",
  "file_ext": "",
  "file_name": "",
  "file_type": "",
  "file_url": "",
  "is_video": 0,
  "origin": "local"
}
```

**Cambio:** El campo `file_information` a nivel raíz ya no se utilizará.

**Nota:** Toda la información de archivos ahora vive exclusivamente dentro del array `content`.

**Impacto:**
- ✅ **Backend:** 
  - No procesar este campo en nuevas creaciones/actualizaciones
  - Migrar datos existentes a `content` si es necesario
- ✅ **Frontend:** 
  - Acceder a información de archivos desde `content[n].file_information`
  - Remover lógica que lea el campo raíz

---

### 7. **vertical_file_information** (nivel raíz) - DEPRECADO ⚠️

```json
"vertical_file_information": {  // ❌ DEPRECADO
  "file_duration": "",
  "file_ext": "",
  "file_name": "",
  "file_type": "",
  "file_url": "",
  "is_video": 0,
  "origin": "local"
}
```

**Cambio:** El campo `vertical_file_information` a nivel raíz ya no se utilizará.

**Impacto:**
- ✅ **Backend:** No procesar este campo
- ✅ **Frontend:** Usar solo `content[n].vertical_file_information`

---

## 📊 Comparación: Antes vs Después

### Estructura Anterior (Deprecated)
```json
{
  "id": "173628",
  "course_id": "2631",
  "date": "2025-03-11",
  "date_update": "2025-11-07 20:39:34",
  "description": "Descripción del source",
  "class_id": "409342523",
  "annex_file": { /* objeto único */ },
  "file_information": { /* info a nivel raíz */ },
  "vertical_file_information": { /* info a nivel raíz */ },
  "language_id": 1, # 1: Español, 2: Inglés, 3: Portugués.
  "name": "Titulo de la clase",
  "order": 1,
  "state": 1,
  "time_add": 8,
  "type": 7, // 0: Introducción 1: Desarrollo, 2: -- , 3: Recurso, 4: Conslusión, 5: -- , 6: -- , 7: Curación.
  "content": [...]
}
```

### Estructura Nueva (Actual)
```json
{
  "id": "173628",
  "course_id": "2631",
  "created_at": "2025-03-11T21:15:23.357205Z",
  "last_update": "2025-11-07 20:39:34",
  "last_update_by": "425034",
  "class_id": "409342523",
  "annex_file": [ /* array de objetos */ ],
  "order": 1,
  "state": 1,
  "time_add": 8,
  "type": 7, // 0: Introducción 1: Desarrollo, 2: -- , 3: Recurso, 4: Conslusión, 5: -- , 6: -- , 7: Curación.
  "content": [...]
  // description, file_information y vertical_file_information removidos
}
```

### Estructura del campo content

```json
[
  {
   "content_type": "text",
   "description": "<p></p><p>Este es un texto que acompaña a la clase</p><p></p>",
   "order": "1"
  },
  {
    "author": {
      "image": "https://d3puay5pkxu9s4.cloudfront.net/courses/2631/img/web/imagen.jpg?a=66",
      "name": "tests",
      "url": ""
    },
    "content_type": "media",
    "file_information": {
      "file_bucket": "",
      "file_date": "2025-03-14T16:09:21.489Z",
      "file_duration": "00:02:19",
      "file_ext": "mp4",
      "file_name": "",
      "file_type": "video/mp4",
      "file_url": "25IfpaK_Xqc",
      "is_video": "1", // ❌ DEPRECADO
      "origin": "youtube"
     },
     "order": "2",
     "vertical_author": {
        "image": "",
        "name": "",
        "url": ""
     },
     "vertical_file_information": {
        "file_bucket": "",
        "file_date": "",
        "file_duration": "01:00:00",
        "file_ext": "",
        "file_name": "",
        "file_type": "",
        "file_url": "",
        "is_video": "0", // ❌ DEPRECADO
        "origin": "local"
     }
    },
  {
   "content_type": "text",
   "description": "<p>Este otro texto complementario</p><p></p>",
   "order": "3"
  },
  {
   "author": {
    "image": "https://edutin.com/assets/images/edutin/edutin-logo.png",
    "name": "Profesor",
    "url": ""
   },
   "content_type": "media",
   "file_information": {
    "file_bucket": "",
    "file_date": "2025-03-14T16:11:15.811Z",
    "file_duration": "03:21:43",
    "file_ext": "mp4",
    "file_name": "",
    "file_type": "video/mp4",
    "file_url": "Tyb9DNE5bNU",
    "origin": "youtube"
   },
   "order": "4",
   "vertical_author": {
    "image": "",
    "name": "",
    "url": ""
   },
   "vertical_file_information": {
    "file_bucket": "",
    "file_date": "",
    "file_duration": "",
    "file_ext": "",
    "file_name": "",
    "file_type": "",
    "file_url": "",
    "origin": "local"
   }
  },
  {
   "content_type": "text",
   "description": "<p></p><p></p><p></p>",
   "order": "5"
  }
 ]
```
---




**Fecha de Documentación:** 8 de Noviembre, 2025  
**Versión:** 1.0

