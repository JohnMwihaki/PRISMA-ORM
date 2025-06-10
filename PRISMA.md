# Prisma ORM

An ORM (Object-Relational Mapping) is a technique or a tool that allows developers to interact with a database using an object-oriented approach, rather than writing SQL queries.

Some examples of ORMs include:

SQLAlchemy (Python),Sequelize (JavaScript),Hibernate (Java),Prisma,Drizzle

**Key Benefits of Using ORM**

- Developer Productivity-ORMs eliminate much of the boilerplate code needed for database operations.
- Database Portability
- Maintenance and Consistency

## What is Prisma ORM?

Prisma is a next-generation Object-Relational Mapping (ORM) and database toolkit for Node.js and TypeScript.it consists of the folowing parts:

- **Prisma Client**: Auto-generated and type-safe query builder for Node.js & TypeScript

- **Prisma Migrate:** Migration system

- **Prisma Studio:** GUI to view and edit data in your database.


## What are Models?
Models represent database tables in your Prisma schema. Each model defines the structure, fields, and relationships of your data.

**Key concepts of Prisma Models Include**

- **Data Models:** Define the tables and their columns  in your database.

- **Field Types:** Specify the data types for each field (e.g., String, Int, Boolean).

- **Relations:** Describe how models relate to each other using relation fields.

- **Attributes:** Add additional metadata to fields and models using attributes like @id, @default, @relation.

**Field Modifiers**

They are used to indicate:

 - If a field is optiona ```?``` or whether a field can contain multiple values ```[]```

*example*
```
model Product {
  id                 Int    @id @default(autoincrement())
  productTitle       String @map("product_title")
  productDescription String @map("product_description")
  productCost        Float  @map("product_cost")
  unitsLeft          Int    @map("units_left")
  @@map("products_table")
}
```

## How do Prisma Works?

Before we use Prisma, we need to install it in our project as an NPM package.

To install Prisma,Run the following command:

```bash
npm install prisma -D
```

or

```bash
npm install prisma --save-dev
```

After installing Prisma, we need to set up our Prisma project.

we run command below

```bash
npx prisma init --datasource-provider DATABASE
```

Where DATABASE is just a placeholder you can replace with the name of ypur database e.g postgresql, hence if you are using postgresql by default you can run the command without the flag
`bash --datasource-provider` flag.

```bash
npm prisma init
```

**Define the Database Schema**

After running the command for initialization we get two important things:

- Creates a new directory called _prisma_ that contains the file **schema.prisma**, which contains our database connection variables and models.

- Creates a **.env** file at the root of the project which is used for defining the environment variables such as the connection string.

**Prisma Schema**

Every project that uses a tool from the Prisma ORM toolkit starts with a Prisma schema. The Prisma schema allows developers to define their application models in an intuitive data modeling language. It also contains the connection to a database and defines a generator:

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User?   @relation(fields: [authorId], references: [id])
  authorId  Int?
}

model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}
```

In this schema, you configure three things:

- Data source: Specifies your database connection.

- Generator: Indicates that you want to generate Prisma Client

- Data model: Defines your application models.

**Connect to the Database**

In the .env file, define your database connection string:

```prisma
DATABASE_URL="postgresql://username:password@localhost:5432/mydatabase"
```

**Run Migrations**

Run the migration to apply the schema to your database:

```bash
npx prisma migrate dev --name init
```

**Generating Prisma Client**

Prisma will generate a type-safe client based on your schema run:

```bash
npx prisma generate
```

If it is not already installed,run the command

```bash
 npm install @prisma/client
```

After querying the database Example:

```javascript
import {PrismaClient} = require('@prisma/client);

async function main(){
    const user= await prisma.user.create(
        data:{
            firstname:'john',
            email:'johnk@gmail.com',
            age:23
        }
    )
    console.log(main());
}

```
### CRUD OPerations
 Prisma provides comprehesive CRUD operations that work with both single records and multiple records.

**Single Record Operations:**

- create() - Create one record
- findUnique() / findFirst() - Read one record
- update() - Update one record
- delete() - Delete one record

**Multiple Record Operations:**

- createMany() - Create multiple records
- findMany() - Read multiple records
- updateMany() - Update multiple records
- deleteMany() - Delete multiple records

**Create Operation**

 *Example of code :*

 ```javascript
 import { PrismaClient } from '@prisma/client';
const client = new PrismaClient();

const createProduct = async () => {
    const newProduct = await client.product.create({
        data: {
            productTitle: "Laptop",
            productDescription: "an awesome laptop",
            productCost: 36000,
            unitsLeft: 10
        }
    })

    console.log(newProduct);
}
```

Using createmany and return 

```javascript
import {PrismaClient } from '@prisma/client';
const cleint=new PrismaClient( log: "Error" "Querry");

async function users(){
    const user_holder=await clent.user.createManyAndReturn({
        data:[
            {
                name:'john kimani',
                email:'johnk@gmail.com',
                age:27
            }
        ]
    })
    console.log(user());
}
```


**Read Operation**

Get all records using ```Findmany()```

```javascript

import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const getProducts = async () => {
  const products = await client.product.findMany();
  console.log(products)
};

getProducts();
```
You can use comparison operators in Prisma in combination with CRUD operations .They include

**Numeric filters**

- equals: matches exactly eg { age: { equals: 25 } }.

- not: matches 'not equal to the specified value' eg { age: { not: 25 } }

- lt: less than eg { age: { lt: 18 } }.

- lte: less than or equal to eg { age: { lte: 18 } }.

- gt: greater than eg { age: { gt: 18 } }.

- gte: greater than or equal to eg { age: { gte: 18 } }.

**String filters**

- equals: matches exactly (case-sensitive) eg { name: { equals: "John" } }

- contains: matches if the string contains the specified substring eg { name: { contains: "John" } }

- startsWith: matches if the string starts with the specified string eg { name: { startsWith: "Jo" } }.

- endsWith: matches if the string ends with the specified string eg ```{ name: { endsWith: "hn" } }.```

- not: negates the condition eg ``` { name: { not: { contains: "Doe" } } }.```


**Update Operation**

 *Example*
```javascript
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

async function products() {
  const updatedProduct = await client.product.update({
    where: { id: 3 },
    data: { productTitle: 'beats by dre headphones' }
  })
  console.log(updatedProduct);
};
```

**Delete Operations**

*Example*

```javascript
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const deletedUser = await prisma.user.delete({
  where: {
    id: 1
  }
})

console.log('Deleted user:', deletedUser)
```

## Relationships

Prisma uses a schema to define relationships making it easier to work with relational databases.

*The types of relationships include:*

- one-to-one relationship (1-1): means that one record in a table is associated to only one record in another table.

- one-to-many relationship (1-n): means that one record in a table can be associated with multiple other records in another table.

- many-to-many relationship (m-n): means that multiple records in one table can be associated to multiple records in another table.

**Examples include**

*One to one realationship*
```prisma
model User {
  id    Int    @id @dafault(uuid())
  posts Post[]
}

model Post {
  id       Int  @id @default(uuid())
  authorId Int
  author   User @relation(fields: [authorId], references: [id])
}
```

*one to many relationship*

```
model Post {
  id         Int        @id
  categories Category[]
}

model Category {
  id    Int    @id
  posts Post[]
}
```
