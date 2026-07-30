# TypeScript/JavaScript DBF Reading Libraries Comparison

## Executive Summary

This document compares TypeScript/JavaScript alternatives to **dbfread2** (Python) for reading DBF files. Based on comprehensive research, **dbffile** (by yortus) is the most powerful and feature-complete JavaScript/TypeScript alternative available.

---

## Quick Comparison Table

| Feature | dbfread2 (Python) | dbffile (JS/TS) | dbf-reader (TS) | node-dbf (JS) |
|---------|-------------------|-----------------|-----------------|---------------|
| **Language** | Python 3.12+ | TypeScript/JavaScript | TypeScript | JavaScript |
| **Field Types** | 18+ types | 10 types | 14 types | 2 types (C, N) |
| **Memo Support** | ✅ Full (FPT, DBT) | ⚠️ Experimental (read-only) | ❌ None | ❌ None |
| **Encoding** | ✅ Full per-file/per-field | ✅ Full per-file/per-field | ⚠️ Limited | ⚠️ Basic UTF-8 |
| **Streaming** | ✅ Yes | ✅ Yes (async iteration) | ❌ Buffer only | ❌ fs.readFile only |
| **Deleted Records** | ✅ Yes | ✅ Yes (configurable) | ❌ Not documented | ✅ Yes (@deleted flag) |
| **Write Support** | ❌ Read-only | ✅ Yes | ❌ Read-only | ❌ Read-only |
| **TypeScript** | ✅ Type hints | ✅ Native TS | ✅ Native TS | ❌ No |
| **Weekly Downloads** | N/A (PyPI) | 4,289 | 1,789 | 280 |
| **Maintenance** | ✅ Active | ⚠️ Last update 1yr ago | ⚠️ 4 years ago | ❌ Unmaintained |
| **GitHub Stars** | N/A | not verified | 5 | not verified |
| **DBF Versions** | dBase III/IV/VFP/FoxBase+ | dBase III/IV/VFP9/FP2 | dBase III/IV/FP/VFP | Limited |

---

## Detailed Library Analysis

### 🏆 1. dbffile (RECOMMENDED)

**GitHub:** https://github.com/yortus/DBFFile
**NPM:** `npm install dbffile`
**Weekly Downloads:** 4,289

#### Supported Field Types (10)
- `C` - Character/String
- `N` - Numeric
- `F` - Float
- `Y` - Currency
- `I` - Integer
- `L` - Logical
- `D` - Date
- `T` - DateTime
- `B` - Double
- `M` - Memo (⚠️ experimental, read-only)

#### Supported DBF Versions
- dBase III (0x83)
- dBase IV (0x8b)
- VFP9 (0x30)
- FoxPro 2 (0xf5)

#### Key Features
✅ **Both read AND write** capabilities
✅ **Async/await** with streaming via async iteration
✅ **Flexible encoding** - per-file or per-field (via iconv-lite)
✅ **Deleted records** - configurable via `includeDeletedRecords` option
✅ **Loose mode** - tolerates unsupported versions/types
✅ **Full TypeScript** support with type definitions
✅ **Batch reading** - `readRecords(maxCount)` for controlled memory usage

#### Example Usage
```typescript
import { DBFFile } from 'dbffile';

// Async iteration (streaming)
const dbf = await DBFFile.open('/path/to/file.dbf');
for await (const record of dbf) {
    console.log(record);
}

// Batch reading
const records = await dbf.readRecords(100);

// With custom encoding
const dbf2 = await DBFFile.open('/path/to/file.dbf', {
    encoding: 'utf-8'
});

// Include deleted records
const dbf3 = await DBFFile.open('/path/to/file.dbf', {
    includeDeletedRecords: true
});
```

#### Limitations
- Memo support is experimental (read-only, cannot write memo fields)
- Field names limited to 10 characters
- Last published 1 year ago (maintenance concern)

#### Verdict
**Best overall choice** for JavaScript/TypeScript projects requiring robust DBF reading with modern async/await patterns.

---

### 2. dbf-reader

**GitHub:** https://github.com/shubhgupta4u/dbf-reader
**NPM:** `npm install dbf-reader`
**Weekly Downloads:** 1,789

#### Supported Field Types (14)
- Character
- Character (binary)
- Currency
- Date
- Datetime
- Double
- Float
- General
- Integer
- Integer (AutoIncrement)
- Logical
- Numeric
- Varchar
- Varchar (binary)

**NOT Supported:** Memo, Memo (binary), Varbinary, Blob

#### Supported DBF Versions
- Clipper/dBase III
- dBase IV
- dBase IV Windows
- Foxpro 2.x
- Visual Foxpro

#### Key Features
✅ **TypeScript native** with full type definitions
✅ **Many field types** (14 types)
❌ **No memo support**
❌ **No streaming** - requires loading entire file into Buffer
⚠️ **No encoding options** documented

#### Example Usage
```typescript
import { Dbf } from 'dbf-reader';
import * as fs from 'fs';

let buffer = fs.readFileSync('/path/sample.dbf');
let datatable = Dbf.read(buffer);

datatable.rows.forEach(row => {
    datatable.columns.forEach(col => {
        console.log(row[col.name]);
    });
});
```

#### Limitations
- **No memo field support** (major limitation)
- **No streaming** - must load entire file into memory
- **Minimal maintenance** - only 10 commits, 5 stars
- Published 4 years ago with no recent updates
- No encoding configuration options documented

#### Verdict
Suitable for **small to medium DBF files without memo fields**. Not recommended for large files or production use due to lack of streaming and minimal maintenance.

---

### 3. node-dbf

**GitHub:** https://github.com/abstractvector/node-dbf
**NPM:** `npm install node-dbf`
**Weekly Downloads:** 280

#### Supported Field Types (2)
- `C` - Character
- `N` - Numeric

**Only 2 types!** Other field types are on the TODO list.

#### Key Features
✅ **Event-based** parser (extends EventEmitter)
✅ **Deleted records** - includes `@deleted` flag
✅ **CLI tool** - convert DBF to CSV
❌ **No TypeScript** support
❌ **No streaming** - uses `fs.readFile`
❌ **No memo support**
⚠️ **Minimal field type support**

#### Example Usage
```javascript
const Parser = require('node-dbf');

const parser = new Parser('/path/to/file.dbf', {
    encoding: 'utf-8'
});

parser.on('start', (p) => {
    console.log('dBase file parsing started');
});

parser.on('header', (h) => {
    console.log('Header:', h);
});

parser.on('record', (record) => {
    console.log('Record:', record);
});

parser.on('end', (p) => {
    console.log('Parsing finished');
});

parser.parse();
```

#### Limitations
- **Unmaintained** - README states: "This library is no longer being actively maintained"
- **Only 2 field types** (Character and Numeric)
- No TypeScript support
- No streaming (on TODO list)
- Published 7 years ago
- Only 280 weekly downloads

#### Verdict
**NOT RECOMMENDED** for new projects. Unmaintained and severely limited field type support.

---

### 4. Forks and Variants

#### @filip96/node-dbf
- **TypeScript rewrite** of node-dbf
- **Visual FoxPro** specific improvements
- Float vs Integer distinction
- Still limited to **Character and Numeric** types only
- 0 stars, 1 fork - minimal adoption

#### dbf-vfpro (by fwiwDev)
- Fork of node-dbf for Visual FoxPro
- Only **Character and Numeric** types
- 0 stars, 0 forks
- Not recommended

---

## Field Type Coverage Comparison

| Field Type | dbfread2 | dbffile | dbf-reader | node-dbf |
|------------|----------|---------|------------|----------|
| C (Character) | ✅ | ✅ | ✅ | ✅ |
| N (Numeric) | ✅ | ✅ | ✅ | ✅ |
| F (Float) | ✅ | ✅ | ✅ | ❌ |
| I (Integer) | ✅ | ✅ | ✅ | ❌ |
| L (Logical) | ✅ | ✅ | ✅ | ❌ |
| D (Date) | ✅ | ✅ | ✅ | ❌ |
| T (DateTime) | ✅ | ✅ | ✅ | ❌ |
| Y (Currency) | ✅ | ✅ | ✅ | ❌ |
| B (Binary/Double) | ✅ | ✅ | ✅ | ❌ |
| M (Memo) | ✅ | ⚠️ (read-only) | ❌ | ❌ |
| O (Double) | ✅ | ❌ | ✅ | ❌ |
| G (OLE Object) | ✅ | ❌ | ✅ | ❌ |
| P (Picture) | ✅ | ❌ | ❌ | ❌ |
| + (AutoIncrement) | ✅ | ❌ | ✅ | ❌ |
| @ (Timestamp) | ✅ | ❌ | ❌ | ❌ |
| V (Varchar) | ✅ | ❌ | ✅ | ❌ |
| 0 (Flags) | ✅ | ❌ | ❌ | ❌ |

---

## Special Purpose Libraries

### shpjs (shapefile-js)
**Purpose:** Shapefile to GeoJSON conversion
**GitHub:** https://github.com/calvinmetcalf/shapefile-js
**Stars:** 794

- **NOT a standalone DBF reader**
- Requires `.shp` file (shapefile required)
- DBF parsing is ancillary to shapefile conversion
- Supports encoding via CPG files
- Actively maintained (latest: v6.2.0, Sep 2025)

**Verdict:** Only use if you're working with shapefiles. Not suitable for general DBF reading.

---

## Recommendations by Use Case

### 🎯 General Purpose DBF Reading
**Use:** `dbffile`
**Why:** Best feature coverage, streaming support, TypeScript native, both read/write

### 🎯 Large File Processing
**Use:** `dbffile`
**Why:** Async iteration and batch reading for memory efficiency

### 🎯 TypeScript Projects
**Use:** `dbffile`
**Why:** Full TypeScript support with type definitions

### 🎯 Memo Field Support
**Use:** `dbffile` (limited) or **stay with Python dbfread2**
**Why:** Only dbffile has any memo support in JS/TS ecosystem, but it's experimental

### 🎯 Small Files, No Memo Fields
**Use:** `dbf-reader` or `dbffile`
**Why:** Either works, but dbffile is more future-proof

### 🎯 Maximum Field Type Coverage
**Use:** **dbfread2 (Python)** - 18+ types
**Why:** No JS/TS library matches Python's field type coverage

---

## Migration Path: Python dbfread2 → JavaScript/TypeScript

If you're considering migrating from dbfread2 to JavaScript/TypeScript:

### ✅ Feasible if:
- You don't heavily rely on memo fields
- Your DBF files use standard field types (C, N, F, I, L, D, T, Y, B)
- You can tolerate dbffile's memo limitations
- You're okay with slightly less mature ecosystem

### ❌ Stay with Python if:
- You need **full memo field support** (FPT/DBT)
- You require **advanced field types** (G, P, 0, @, etc.)
- You need **maximum reliability** and battle-tested code
- You work with complex Visual FoxPro databases

---

## Conclusion

### The Winner: **dbffile** 🏆

For JavaScript/TypeScript projects, **dbffile** is the clear winner with:
- 10 field types (vs 2-14 in alternatives)
- Streaming via async/await
- Both read and write capabilities
- TypeScript native
- 4,289 weekly downloads
- Best-maintained among JS/TS alternatives

### However...

**dbfread2 (Python) remains superior** for:
- Full memo field support (FPT/DBT)
- 18+ field types vs 10 in dbffile
- More mature and tested codebase
- Active maintenance
- Comprehensive documentation

### Recommendation

- **New JS/TS projects:** Use `dbffile`
- **Python projects:** Use `dbfread2`
- **Need maximum compatibility:** Use `dbfread2`
- **Need to write DBF files in JS/TS:** Use `dbffile`

---

## NPM Package Popularity (Weekly Downloads)

1. **dbffile:** 4,289 downloads/week
2. **dbf-reader:** 1,789 downloads/week
3. **node-dbf:** 280 downloads/week
4. **Forks/variants:** <100 downloads/week

---

## Additional Resources

### Official Documentation
- dbffile: https://github.com/yortus/DBFFile
- dbf-reader: https://github.com/shubhgupta4u/dbf-reader
- node-dbf: https://github.com/abstractvector/node-dbf
- shpjs: https://github.com/calvinmetcalf/shapefile-js

### DBF Specifications
- dBase III: http://www.dbase.com/
- Visual FoxPro: https://docs.microsoft.com/en-us/previous-versions/visualstudio/foxpro/

### Comparison Tools
- NPM Trends: https://npmtrends.com/dbffile-vs-dbf-reader-vs-node-dbf
- NPM Charts: https://npmcharts.com/

---

**Last Updated:** 2026-07-30
**Note:** Download counts, version numbers, and "last published" dates were gathered
from web search snapshots at the time of writing and should be re-checked against
npm before relying on them.
**Researched for:** dbfread2 project
**Maintainer:** Nutchanon Ninyawee
