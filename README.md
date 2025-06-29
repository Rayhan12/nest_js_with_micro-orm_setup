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

# 2. Run the migration
npx mikro-orm migration:up

# 3. Restart your application
npm run start:dev
```
