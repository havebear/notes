`class-validator` 是一个基于装饰器的 TypeScript/JavaScript 对象验证库，常用于验证前端表单数据或后端接口参数。它通过装饰器（如 `@IsString()`, `@IsEmail()` 等）声明式地定义验证规则，并支持自定义验证逻辑。

---

## 介绍
### 核心特性

1. **装饰器驱动**：使用装饰器简洁声明规则。
2. **内置验证器**：支持常见类型（字符串、数字、数组等）、格式（邮箱、URL、UUID）和业务逻辑（长度、范围、枚举）。
3. **异步验证**：支持自定义异步验证器（如检查数据库唯一性）。
4. **嵌套验证**：验证嵌套对象或数组。
5. **错误信息定制**：可本地化或自定义错误消息。

---
### 快速开始

#### 1. 安装

```bash
npm install class-validator reflect-metadata
```

#### 2. 基本使用

```typescript
import 'reflect-metadata';
import { IsEmail, IsString, Length, validate } from 'class-validator';

class User {
  @IsString()
  @Length(2, 20, { message: '用户名长度需为2-20字符' })
  name: string;

  @IsEmail({}, { message: '无效的邮箱格式' })
  email: string;
}

// 验证示例
const user = new User();
user.name = 'A'; // 触发错误
user.email = 'invalid-email'; // 触发错误

validate(user).then(errors => {
  if (errors.length > 0) {
    console.log('验证失败:', errors);
  } else {
    console.log('验证通过!');
  }
});
```

---

### 常用装饰器

- **类型验证**：`@IsString()`, `@IsNumber()`, `@IsBoolean()`
- **范围验证**：`@Length(min, max)`, `@Min(value)`, `@Max(value)`
- **格式验证**：`@IsEmail()`, `@IsUrl()`, `@IsUUID()`
- **数组验证**：`@IsArray()`, `@ArrayMinSize(min)`
- **嵌套验证**：`@ValidateNested()` + `@Type(() => Class)`
- **条件验证**：`@ValidateIf(obj => obj.condition)`

---
### 进阶用法

#### 自定义验证器

```typescript
import { ValidatorConstraint, ValidatorConstraintInterface } from 'class-validator';

@ValidatorConstraint({ async: true })
class IsUserAlreadyExist implements ValidatorConstraintInterface {
  async validate(email: string) {
    // 模拟数据库查询
    return !await UserService.checkEmailExists(email);
  }
  defaultMessage() { return '邮箱已被注册'; }
}

// 使用
class CreateUserDto {
  @IsEmail()
  @Validate(IsUserAlreadyExist)
  email: string;
}
```

#### 嵌套验证

```typescript
import { Type } from 'class-transformer';

class Address {
  @IsString()
  city: string;
}

class User {
  @ValidateNested()
  @Type(() => Address)
  address: Address;
}
```

---

### 集成框架

- **NestJS**：通过 `@UsePipes(ValidationPipe)` 自动验证。
- **Express**：手动调用 `validate()` 并处理错误。
    

---

### 注意事项

- 确保启用 `reflect-metadata`（在入口文件顶部 `import 'reflect-metadata'`）。
- 对于嵌套对象，需配合 `class-transformer` 的 `@Type` 装饰器正确转换类型。

## 类型

### 1. 类型检查

|装饰器|说明|示例|
|:--|:--|:--|
|`@IsDefined()`|值不能为 `undefined`|`@IsDefined()`|
|`@IsOptional()`|允许 `undefined`/`null`，其余规则跳过|`@IsOptional()`|
|`@Allow()`|显式放行，不做任何验证|`@Allow()`|
|`@IsEmpty()`|必须为空字符串、null 或 undefined|`@IsEmpty()`|
|`@IsNotEmpty()`|与上相反|`@IsNotEmpty()`|

---

### 2. 基础类型

|装饰器|说明|示例|
|:--|:--|:--|
|`@IsString()`|字符串|`@IsString()`|
|`@IsNumber()`|数字|`@IsNumber({ maxDecimalPlaces: 2 })`|
|`@IsInt()`|整数|`@IsInt()`|
|`@IsBoolean()`|布尔|`@IsBoolean()`|
|`@IsDate()`|Date 实例|`@IsDate()`|
|`@IsArray()`|数组|`@IsArray()`|
|`@IsObject()`|纯对象|`@IsObject()`|
|`@IsEnum(entity)`|枚举值|`@IsEnum(UserRole)`|

---

### 3. 数值范围

|装饰器|说明|示例|
|:--|:--|:--|
|`@Min(value)`|最小值|`@Min(0)`|
|`@Max(value)`|最大值|`@Max(100)`|
|`@IsPositive()`|正数|`@IsPositive()`|
|`@IsNegative()`|负数|`@IsNegative()`|

---

### 4. 字符串长度 & 子串

|装饰器|说明|示例|
|:--|:--|:--|
|`@Length(min, max)`|字符串长度|`@Length(1, 20)`|
|`@MinLength(min)`|最短长度|`@MinLength(6)`|
|`@MaxLength(max)`|最长长度|`@MaxLength(255)`|
|`@Contains(seed)`|必须包含子串|`@Contains('hello')`|
|`@NotContains(seed)`|不能包含子串|`@NotContains('admin')`|

---

### 5. 格式校验

|装饰器|说明|示例|
|:--|:--|:--|
|`@IsEmail(options)`|邮箱|`@IsEmail()`|
|`@IsUrl(options)`|URL|`@IsUrl({ protocols: ['https'] })`|
|`@IsUUID(version?)`|UUID|`@IsUUID(4)`|
|`@IsJSON()`|合法 JSON 字符串|`@IsJSON()`|
|`@IsJWT()`|JWT 字符串|`@IsJWT()`|
|`@IsCreditCard()`|信用卡号|`@IsCreditCard()`|
|`@IsPhoneNumber(region)`|手机号|`@IsPhoneNumber('CN')`|
|`@IsPostalCode(locale)`|邮政编码|`@IsPostalCode('CN')`|
|`@IsMimeType()`|MIME 类型|`@IsMimeType()`|
|`@IsISO8601()`|ISO8601 日期|`@IsISO8601()`|
|`@IsDateString()`|ISO 日期字符串|`@IsDateString()`|
|`@IsBase64()`|Base64|`@IsBase64()`|
|`@IsHexColor()`|十六进制颜色|`@IsHexColor()`|
|`@IsMACAddress()`|MAC 地址|`@IsMACAddress()`|
|`@IsIP(version?)`|IP|`@IsIP(4)`|
|`@IsISBN(version?)`|ISBN|`@IsISBN(13)`|
|`@IsEAN()`|EAN 条形码|`@IsEAN()`|
|`@IsEthereumAddress()`|ETH 地址|`@IsEthereumAddress()`|
|`@IsBtcAddress()`|比特币地址|`@IsBtcAddress()`|

---

### 6. 数组专用

|装饰器|说明|示例|
|:--|:--|:--|
|`@ArrayMinSize(min)`|最小元素数|`@ArrayMinSize(1)`|
|`@ArrayMaxSize(max)`|最大元素数|`@ArrayMaxSize(5)`|
|`@ArrayUnique(identifier?)`|元素唯一|`@ArrayUnique('id')`|
|`@IsIn(values[])`|值必须在给定数组|`@IsIn(['admin', 'user'])`|
|`@IsNotIn(values[])`|值不能在给定数组|`@IsNotIn(['root'])`|

---

### 7. 对象/嵌套

|装饰器|说明|示例|
|:--|:--|:--|
|`@ValidateNested()`|递归验证子对象|`@ValidateNested()`|
|`@Type(() => Class)`|配合 `class-transformer` 做类型转换|`@Type(() => Address)`|

---

### 8. 条件验证

|装饰器|说明|示例|
|:--|:--|:--|
|`@ValidateIf(fn)`|条件满足才验证|`@ValidateIf(o => o.role === 'admin') @IsString()`|
|`@ValidateNested()` + `@IsOptional()`|可选嵌套对象||

---

### 9. 自定义验证器

|装饰器|说明|示例|
|:--|:--|:--|
|`@Validate(ConstraintClass, ...args)`|使用自定义验证类|`@Validate(IsUserAlreadyExist)`|
|`@IsDefined()` + `@ValidatePromise()`|异步验证 Promise||

---

### 10. 文件上传（额外包）

|装饰器|说明|需要额外包|
|:--|:--|:--|
|`@IsFile()`|是文件对象|`class-validator-multer`|
|`@MaxFileSize(bytes)`|文件大小限制|同上|
|`@HasMimeType(mimes[])`|MIME 类型|同上|

---

### 速查表（常用 Top 20）

```
@IsString   @IsNumber   @IsInt   @IsBoolean   @IsArray
@IsEnum     @IsEmail    @IsUrl   @IsUUID      @IsDate
@Length     @Min        @Max     @IsPositive  @IsOptional
@IsNotEmpty @IsIn       @ArrayMinSize  @ValidateNested  @IsJSON
@IsJWT      @IsPhoneNumber
```


## 问题

### @IsString({ each: true })

**把被装饰的字段当成“字符串数组”来校验**，要求**数组里的每一个元素都必须是 string**；如果其中任何一项不是 string，就会报错。  
字段本身允许是空数组 `[]`，也允许元素是空字符串 `""` —— 只要每个元素类型是 string 即可。

#### 1. 基本用法

```ts
import { IsString } from 'class-validator';

class TagDto {
  // ✅ 正确：['js', 'ts']   ✅ ['']   ✅ []  
  // ❌ 错误：[1, 'js'] 或 ['js', null]
  @IsString({ each: true })
  tags?: string[];
}
```

---

#### 2. 再加长度校验（每个元素 1-10 字符）

```ts
import { ArrayMinSize, ArrayMaxSize, IsString, Length } from 'class-validator';

class TagDto {
  @ArrayMinSize(1)               // 数组至少 1 个元素
  @ArrayMaxSize(5)               // 最多 5 个元素
  @IsString({ each: true })
  @Length(1, 10, { each: true }) // 每个元素 1-10 字符
  tags: string[];
}
```

---

#### 3. 常见疑问

|输入|通过？|原因|
|:--|:--|:--|
|`{ tags: [] }`|✅|空数组合法|
|`{ tags: [''] }`|✅|空字符串也是 string|
|`{ tags: ['a', 123] }`|❌|第二项不是 string|
|`{ tags: null }`|❌|null 不是数组|
|`{ tags: 'abc' }`|❌|不是数组，each 校验无效|

---

#### 4. 注意事项

- 如果字段可能不是数组，先用 `@IsArray()` 兜底：
    
    ```ts
    @IsArray()
    @IsString({ each: true })
    tags: string[];
    ```
    
- 想“允许空字符串但禁止 null/undefined”，参考上一答即可，对数组同样适用。
    

一句话总结  
`@IsString({ each: true })` 就是“数组里每个元素必须是 string”，其它规则（长度、非空等）都可以通过 `{ each: true }` 或额外的装饰器叠加。