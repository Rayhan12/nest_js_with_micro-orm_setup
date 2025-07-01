# nest_js_with_micro-orm_setup

## Init
```bash 
npm i @mikro-orm/core @mikro-orm/nestjs @mikro-orm/sqlite
```

## APP MODULE
```ts
@Module({
  imports: [
      MikroOrmModule.forRoot(),
      PostModule,
      UserModule,
    ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## package.json File
add this at the bottom 
```json
  "mikro-orm": {
    "useTsNode": true,
    "configPaths": [
      "./mikro-orm.config.ts"
    ]
  }

```

## `micro-orm.config.ts` File
```ts
import { User } from 'src/user/entities/user.entity';
import { Post } from 'src/post/entities/post.entity';
import { Options } from '@mikro-orm/core';
import { TsMorphMetadataProvider } from '@mikro-orm/reflection';
import { SqliteDriver } from '@mikro-orm/sqlite';

const config: Options = {
  dbName: 'api_database.sqlite', // Or an absolute path
//   entities: [User,Post], 
//   entitiesTs: [User,Post], 
  entities: ['./dist/**/*.entity.js'],
  entitiesTs: ['./src/**/*.entity.ts'],
  metadataProvider: TsMorphMetadataProvider,
  debug: true, // Enable for detailed logging
  driver: SqliteDriver,
};

export default config;
```

## How to generate migration file and up it? 
```bash
# 1. Generate initial migration
npx mikro-orm migration:create --initial

# IF ITS NOT THE FIRST TIME
npx mikro-orm migration:create --name <custome name>

# 2. Run the migration
npx mikro-orm migration:up

# 3. Restart your application
npm run start:dev
```

## SERVE STATIC FILE
```bash
npm install --save @nestjs/serve-static
```

config `app.module.ts`
```ts
import { Module } from '@nestjs/common';
import { ServeStaticModule } from '@nestjs/serve-static';
import { join } from 'path';

@Module({
  imports: [
    ServeStaticModule.forRoot({
      rootPath: join(__dirname, '..', 'uploads'),
      serveRoot: '/uploads', // this URL prefix maps to the folder
    }),
    // ... your other modules
  ],
})
export class AppModule {}
```

## ADD SWAGGER
```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setGlobalPrefix('api');

  const config = new DocumentBuilder()
    .setTitle('Your API Title')
    .setDescription('Auto-generated API docs')
    .setVersion('1.0')
    .addBearerAuth() // if using JWT
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api-docs', app, document); 

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

## CMD BLOCKS
```bash
 npm install --save @mikro-orm/core @mikro-orm/migrations @mikro-orm/cli @mikro-orm/nestjs
```

If you want to recursively remove quarantine from the whole node_modules (safer for CLI tools):
```bash
xattr -dr com.apple.quarantine node_modules
```

```bash
xattr -d com.apple.quarantine node_modules/.bin/mikro-orm
```

## NOTES
- 🔖 When building entity, if you bring in other entity pull it like this `../../user/entities/user.entity`
