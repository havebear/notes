## 初始化

下面是一份「**可直接照抄**」的 NestJS + Swagger 集成指南（2024-08 最新版），含安装、配置、注解、认证、常见坑，**5 分钟跑通**。

---

### 1️⃣ 安装依赖

```bash
pnpm add @nestjs/swagger swagger-ui-express     # 推荐 pnpm / yarn / npm
```

---

### 2️⃣ 在 `main.ts` 里一次性配置

```ts
import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle('后台接口文档')
    .setDescription('NestJS + Swagger 自动生成')
    .setVersion('1.0')
    .addBearerAuth({ type: 'http', scheme: 'bearer', bearerFormat: 'JWT' }) // JWT 鉴权
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api', app, document); // 访问路径 /api

  await app.listen(3000);
}
bootstrap();
```

---

### 3️⃣ 在控制器 & DTO 上加注解

```ts
@ApiTags('用户')                    // 分组
@Controller('users')
export class UsersController {
  @Post()
  @ApiOperation({ summary: '创建用户' })
  @ApiBody({ type: CreateUserDto })
  @ApiResponse({ status: 201, description: '创建成功' })
  create(@Body() dto: CreateUserDto) {
    return this.service.create(dto);
  }
}
```

```ts
// dto/create-user.dto.ts
export class CreateUserDto {
  @ApiProperty({ description: '用户名', example: 'Tom' })
  readonly name: string;

  @ApiProperty({ description: '年龄', minimum: 0, maximum: 120 })
  readonly age: number;
}
```

---

### 4️⃣ 运行 & 访问

```bash
pnpm start:dev
```

浏览器打开 [http://localhost:3000/api](http://localhost:3000/api) 即见文档。

---

### 5️⃣ 常见补充

|需求|做法|
|:--|:--|
|隐藏接口|`@ApiExcludeEndpoint()`|
|排序端点|`.setOperationsSorter((a, b) => a.operationId.localeCompare(b.operationId))`|
|自定义 CSS|`SwaggerModule.setup('api', app, document, { customCss: './swagger.css' })`|

---

### ✅ 一句话总结

装包 → `main.ts` 配置 → 控制器加 `@Api*` → 重启即可。  
官方迁移器：`sass-migrator module --migrate-deps src/main.scss` 可自动处理旧 `@import` 警告

## 显示 swagger.json 地址

### 方案 1：官方插件自带的「地址栏」

**NestJS ≥ 9**  
`@nestjs/swagger` 2.2+ 已经内置右上角 **“/api-json”** 链接，无需额外代码。

```ts
SwaggerModule.setup('api', app, document, {
  // 第二个对象就是 Swagger UI 选项
  customSiteTitle: '后台 API',
  swaggerOptions: {
    // 把 swagger.json 地址直接写在地址栏
    url: '/api-json',
  },
})
```

浏览器访问 `http://localhost:3000/api` 即可看到：

```
Swagger UI
http://localhost:3000/api-json
```

---

### ✅ 方案 2：自定义顶部横幅（完全自定义）

如果你想把地址放在页面**最顶部横幅**里，可注入一段简单 HTML：

```ts
SwaggerModule.setup('api', app, document, {
  customJs: [
    `fetch('/api-json').then(r => r.json()).then(d => {
      const banner = document.createElement('div');
      banner.style.cssText = 'padding:8px;background:#fafafa;border-bottom:1px solid #e4e4e4;font-family:monospace;font-size:12px';
      banner.textContent = 'swagger.json → ' + location.origin + '/api-json';
      document.body.insertBefore(banner, document.body.firstChild);
    });`,
  ],
})
```

效果：页面顶部出现一条灰色横幅，**直接复制 swagger.json 地址**。

---

### ✅ 一句话总结

官方已自带 `/api-json` 链接；若想更显眼，用 `swaggerOptions.url` 或 `customJs` 注入即可