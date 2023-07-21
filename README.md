# TypeCSV: Typesafe Flat File Parsing

Inspired by [Prisma](https://prisma.id) and powered by [Zod](https://zod.dev/), TypeCSV empowers developers to define strongly-typed run-time schemas to which a flat file or string containing structured data must conform.

The core library is nothing magical - in fact, it's primarily a wrapper around [Zod](https://zod.dev/) & [Fast-CSV](https://github.com/C2FO/fast-csv/) that provides a consistent API for parsing csv files or strings then validating their contents against a schema.

The larger & more exciting project is to create an ecosystem of schemas for commonly-used CSV file structures, so that developers can simply `npm install` a schema for the file they're trying to parse, and get a strongly-typed object in return.

The number of potential schemas rounds to infinite, and the more of them that are out there, the more useful this project becomes. If you use this library, please add a new schema to the [TypeCSV Schemas Repo](https://github.com/typecsv/schemas) to help future developers.

## Installation

TypeCSV is available on [npm](https://www.npmjs.com/package/@typecsv/core):

```bash
npm install @typecsv/core
```

The core library (in this repo) has everything you need to define your own schemas & parse .csv strings.

If you're parsing a commonly-used .csv file, you might benefit from searching for a pre-defined schema in the [TypeCSV Schemas](https://github.com/typecsv/schemas) repo. If you find one that works for you, you can install it from npm:

For instance, this is how you'd install the schema to parse the [FDIC's Failed Bank List](https://catalog.data.gov/dataset/fdic-failed-bank-list).

```bash
npm install @typecsv/fdic-failed-bank-list
```

## Usage

### Defining a Schema

A schema is simply a zod tuple that defines the structure of a single .csv row. The values of the tuple are [Zod](https://zod.dev/) schemas that compose to define the type of each column in the row.

Consider the following "csv file" from a hypothetical user export (formatted as a gfm table here).

| name  | age | city        | Registered |
| ----- | --- | ----------- | ---------- |
| John  | 30  | New York    | 2022-01-01 |
| Jane  | 25  | Los Angeles | 2022-04-28 |
| Johan | 28  | Zurich      | 2022-06-03 |

The schema for this csv might look like this:

```js
import { z } from "zod";

const schema = z.tuple([
  z.string().refine((s) => s.length > 0, { message: "Name cannot be empty" }),
  z.number().int().positive(),
  z.string(),
  z.date(),
]);
```
