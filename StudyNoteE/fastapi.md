```python
@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip: skip + limit]
```

什么是：Pydantic 模型

```python
 **item.dict()是什么意思
```

```
q: Optional[str] = Query(None)
```

...使参数可选，与：

```
q: Optional[str] = None
```

但它明确声明它是一个查询参数。

这句话没看懂



没看到Optional中的Query()中的title在哪



![image-20210708225053401](img/image-20210708225053401.png)

这个 * 没搞懂是什么意思



- 当一个请求有多个主题参数的时候，fastapi会使用参数名称作为主体中的键（字段名称）

- body可以让主题拥有另外一个键，也就是说当两个主题的时候他们回在一个主题里面，然后再加一个查询参数人，如果要 放在主题里面而不是查询参数的话，那么就用body标识

- 大于小于或等于里面的等于le ge gt 的意义不懂

- query就是对查询参数进行修饰，path就是对路径参数进行修饰，body就是对主题参数进行修饰。

- embed是body里面的参数，让他等于true的意义就是当传参数的时候必须是一个一个带有键的 JSON`item`并且其中包含模型内容

- optional就是可选参数，我觉得可以用在主题参数和查询参数里面。

- field用再baseModel类里面和query等起的作用好像差不多。

- field是直接从pydantic中间来的，而不是fastapi里面的（query,path,body）

- list 是typing包里面的

- ```python
  tags: List[str] = []
  ```

- 然后因为列表中的类型有些是不能重复的，所以set这个东西就出来了，即使收到带有重复数据的请求，它也会被转换为一组唯一的项目。

  每当输出该数据时，即使源有重复项，它也会作为一组唯一项输出。

- 嵌套模型就是再传入的主题中还有另一个主体，也就是说这个主题的属性之一是另一个主体。

- HttpUrl是pydantic包里面的，然后url用这种类型，该字符串将被检查为一个有效的 URL，并记录在 JSON Schema/OpenAPI 中。

- example这个主体里面加额外信息这个不知道是在干什么。

- 时间类型的数据没有具体是用还是琢磨不清他的是用方法

- cookie的那部分是怎么用的，为什么用起来和普通查询参数一样

- 如果由于某种原因需要禁用下划线`_`到连字符`-`的自动转换，需要将Header的参数convert_underscores设置为False：

- 如果想要获取cookie参数或者请求头里面的参数就直接

  ```python
  @app.get("/cookie/")
  async def read_item(
          *,
          ads_id: str = Cookie(None)
  ):
      return {"ads_id": ads_id}
  ```

  想要获取header里面的参数就把cookie改成header,这两个全部都是在fastapi包下的。

- 响应类型![image-20210709175501524](img/image-20210709175501524.png)

- ![image-20210709175811269](img/image-20210709175811269.png)

- 密码的问题导致输入输出的模型有很多相似，仅仅就是密码的有无![image-20210709180759070](img/image-20210709180759070-1625912580573.png)

- None表示没有，但他是一个具体的值。可以用来初始化变量。Null表示空，他不是一个值，![image-20210709181314723](img/image-20210709181314723.png)

- 响应模型的时候出错了，源码复制粘贴报错，那个响应时隐藏密码，两个模型，一个输入一个输出，报错。

- 经过我的检测应该是EmailStr这个组件有问题，然后输入模型和输出模型公共部分，键的名字要一样。

- 响应模型的时候给模型赋于默认值的时候为什么可以给tax赋值，不可以给price赋值。-->因为给tax赋值之后，price就一定要手动的赋值，因为price没有被赋于默认值，而tax被赋予了默认值，所以tax就不需要每次都赋值了，但是不复制的话传出去也就没有值，因为这些默认值不会包含在响应中，只会包含实际设置的值。

- ```python
  response_model_exclude_unset=True
  ```

  设置这个参数就可以包含默认值

  这个默认值到底有什么用呢

## 响应模型

`response_model_include`

只输出模型中的这些结构

`response_model_exclude`

不输出模型中的这些结构

```python
@app.get(
    "/items/{item_id}/name",
    response_model=Item,
    response_model_include={"name", "description"}
)
async def read_item_name(item_id: str):
    return items[item_id]


@app.get(
    "/items/{item_id}/public",
    response_model=Item,
    response_model_exclude={"tax"}
)
async def read_item_public(
        item_id: str
):
    return items[item_id]
```

## 额外型号

- 永远不要存储用户的明文密码，始终存储一个”安全哈希“,然后可以对其进行验证

- pydantic 有一个  .dict()返回dict模型数据的方法

- 如果我们使用一个dict 并将它传递给一个函数（或类）**user_dict，Python将”解开它”。他将直接将键和值user_dict作为键值参数传递。

- ```python
  UserInDB(**user_dict)
  ```

  ```python
  UserInDB(
      username = user_dict["username"],
      password = user_dict["password"],
      email = user_dict["email"],
      full_name = user_dict["full_name"],
  )
  ```

- ```python
  def fake_save_user(user_in: UserIn)
      hashed_password = fake_password_hasher(user_in.password)
      user_in_db = UserInDB(**user_in.dict(),hashed_password=hashed_password)
      print("User saved! ..not really")
      return user_in_db
  ```

  ```python
  user_in_db = UserInDB(**user_in.dict(),hashed_password=hashed_password)
  ```

**减少重复代码**

```python
from fastapi import FastAPI
from pydantic import BaseModel
from typing import Optional

app = FastAPI()


class UserBase(BaseModel):
    username: str
    email: str
    full_name: Optional[str] = None


class UserIn(UserBase):
    password: str


class UserOut(UserBase):
    pass


class UserInDB(UserBase):
    hashed_password: str


def fake_password_hasher(raw_password: str):
    return "supersecret" + raw_password


def fake_save_user(user_in: UserIn):
    hashed_password = fake_password_hasher(user_in.password)
    user_in_db = UserInDB(**user_in.dict(), hashed_password=hashed_password)
    return user_in_db


@app.post("/user/", response_model=UserOut)
async def read_item(
        user_in: UserIn
):
    user_in_db = fake_save_user(user_in)
    print(user_in_db.dict())
    return user_in_db
```

用继承的方法，子类继承父类



**是用Union可以将响应的类型声明为两种类型，这意味着响应可以是这两种类型中的任何一种。**

```python
from fastapi import FastAPI
from pydantic import BaseModel
from typing import Union

app = FastAPI()


class BaseItem(BaseModel):
    description: str
    type: str


class CarItem(BaseItem):
    type = "car"


class PlaneItem(BaseItem):
    type = "plane"
    size: int


items = {
    "item1": {"description": "All my friends driver a low rider", "type": "car"},
    "item2": {
        "description": "Music is my aeroplane , it's my aeroplane",
        "type": "plane",
        "size": 5
    }
}


@app.get("/items/{item_id}", response_model=Union[PlaneItem, CarItem])
async def read_items(
        item_id: str
):
    return items[item_id]
```

response_model=Union[PlaneItem, CarItem])

在这个里面，planItem比caritem多一个元素，然而把caritem放在前面位置的话就会出现打印planitem少一个元素。

**返回同一种类型的数据列表**

```python
from fastapi import FastAPI
from pydantic import BaseModel
from typing import List

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str


items = [
    {"name": "foo", "description": "这是foo的description"},
    {"name": "bar", "description": "这是bar的description"}
]


@app.get("/items/", response_model=List[Item])
async def read_items():
    return items
```

随心所欲的回应：

```python
@app.get("/item1/", response_model=Dict[str, float])
async def read_items(
        tax: float,
        price: float
):
    a = {"tax": tax, "price": price}
    print(a)
    return a
```

这个里面使用到typing包下的dict

[str , float]分别表示数据类型{string: float, string : float }

## 响应状态码

- `100`以上是“信息”。你很少直接使用它们。具有这些状态代码的响应不能有正文。

- `200`

  以上是“成功”的回应。这些是您最常使用的。

  - `200` 是默认状态代码，这意味着一切都“正常”。
  - 另一个例子是`201`“创建”。它通常在数据库中创建新记录后使用。
  - 一个特例是`204`“无内容”。当没有内容返回给客户端时使用此响应，因此响应不能有正文。

- **`300`**以上是“重定向”。具有这些状态代码的响应可能有也可能没有正文，除了`304`“未修改”，它不能有正文。

- `400`

  及以上用于“客户端错误”响应。这些是您可能最常使用的第二种类型。

  - 例如`404`，对于“未找到”响应。
  - 对于来自客户端的一般错误，您可以使用`400`.

- `500`以上是服务器错误。你几乎从不直接使用它们。当应用程序代码或服务器的某些部分出现问题时，它会自动返回这些状态代码之一。

- 可以使用`fastapi.status`.

- ```python
  from fastapi import FastAPI, status
  
  
  app = FastAPI()
  
  
  
  @app.post("/items/", status_code=status.HTTP_201_CREATED)
  
  async def create_item(name: str):
      return {"name": name}
  
  ```

## 表单数据

- 查询参数用Query
- 主体参数用Body
- 路径参数用Path
- 表单参数用Form
- Header用于获取请求头信息
- Cookie用于获取cookie

这四个都是在fastapi包下的

Form直接继承自Body

Form需要显示的使用，如果没有他，参数将会被解释为查询参数或者主体参数

**请求文件**
文件参数用File

File直接继承于Form

如果将*路径操作函数*参数的类型声明为`bytes`，**FastAPI**将为您读取文件，您将收到内容为`bytes`.

这意味着整个内容将存储在内存中。这将适用于小文件。

在某些情况下，使用`UploadFile`.

UploadFile类型优点（相比于bytes）：

- 它使用“假脱机”文件：
  - 存储在内存中的文件最大大小限制，超过此限制后，它将存储在磁盘中。
- 这意味着它可以很好地处理大文件，如图像、视频、大型二进制文件等，而不会消耗所有内存。
- 您可以从上传的文件中获取元数据。
- 它有一个[类似文件的](https://docs.python.org/3/glossary.html#term-file-like-object) `async`界面。
- 它公开了一个实际的 Python[`SpooledTemporaryFile`](https://docs.python.org/3/library/tempfile.html#tempfile.SpooledTemporaryFile)对象，您可以将其直接传递给其他需要类似文件的对象的库。

UploadFile具有以下属性：

- `filename`: A`str`带有上传的原始文件名（例如`myimage.jpg`）。
- `content_type`:A`str`与内容类型（MIME 类型/媒体类型）（例如`image/jpeg`）。
- `file`:A [`SpooledTemporaryFile`](https://docs.python.org/3/library/tempfile.html#tempfile.SpooledTemporaryFile)（类[文件](https://docs.python.org/3/glossary.html#term-file-like-object)对象）。这是实际的 Python 文件，您可以将其直接传递给其他需要“类文件”对象的函数或库。

`UploadFile`有以下`async`方法。它们都在下面调用相应的文件方法（使用内部`SpooledTemporaryFile`）。

- `write(data)`: 写入`data`(`str`或`bytes`) 到文件。
- `read(size)`：读取文件的`size`( `int`) 个字节/字符。
- `seek(offset)`: 转到文件中的字节位置`offset`( `int`)。
  - 例如，`await myfile.seek(0)`会转到文件的开头。
  - 如果您运行`await myfile.read()`一次然后需要再次阅读内容，这将特别有用。
- `close()`: 关闭文件。

由于所有这些方法都是`async`方法，因此您需要“等待”它们

例如，在`async` *路径操作函数中，*您可以通过以下方式获取内容：

```python
contents = await myfile.read()
```

如果你在一个普通的`def` *路径操作函数里面*，你可以`UploadFile.file`直接访问，例如：

```python
contents = myfile.file.read()
```

**FastAPI**的`UploadFile`直接继承自**Starlette**的`UploadFile`，但添加了一些必要的部分，使其与**Pydantic**和**FastAPI**的其他部分兼容。

==可以在*路径操作中*声明多个`File`和`Form`参数，但也不能声明您希望以 JSON 形式接收的字段，因为请求的正文将使用代替 进行编码。`Body``multipart/form-data``application/json`==

**多文件上传**

```python
from fastapi import FastAPI, File, UploadFile
from typing import List
from fastapi.responses import HTMLResponse

app = FastAPI()


@app.post("/files/")
async def create_files(
        files: List[bytes] = File(...)
):
    return {"file_size": [len(file) for file in files]}


@app.post("/uploadfile/")
async def create_upload_files(
        files: List[UploadFile] = File(...)
):
    return {"filenames": [file.filename for file in files]}


@app.get("/")
async def main():
    content = """
<body>
<form action="/files/" enctype="multipart/form-data" method="post">
<input name="files" type="file" multiple>
<input type="submit">
</form>
<form action="/uploadfiles/" enctype="multipart/form-data" method="post">
<input name="files" type="file" multiple>
<input type="submit">
</form>
</body>
    """
    return HTMLResponse(content=content)
```



## 遍历列表

for循环：

```python
lists = ["m1", 1900, "m2", 2000]
for item in lists:
print(item)
```

while循环：

```python
lists = ["m1", 1900, "m2", 2000]
count = 0
while count < len(lists):
print(lists[count])
  count = count + 1
```

使用iter():

```python
for val in iter(lists):
 
  print(val)
```

索引遍历：

```python
for index in range(len(lists)):
 
  print(lists[index])
```

enumerate遍历方法：

```python
for i, val in enumerate(lists):
 
  print(i, val)
```

**表格和文件**

## 处理错误

在很多情况下，需要使用API的客户端通知错误

该客户端可以是带有前端的浏览器，来自其他人的代码，物联网设备等。

可能需要告诉客户：

- 客户端没有足够的权限进行该操作。
- 客户端无权访问该资源。
- 客户端尝试访问的资源不存在
- 等等

要将带有错误的 HTTP 响应返回给您使用的客户端`HTTPException`。

HTTPException是一个普通的Python异常，带有与API相关的 附加数据。

因为它是一个附加数据，所以你不是return它，而是raise它。

这也就意味着，当遇到错误时，他不会运行函数中其余代码，他将立即终止请求并将HTTP错误发送到客户端。

```python
from fastapi import FastAPI,HTTPException


app = FastAPI()


items = {"foo": "The Foo Wrestlers"}


@app.get("/items/{item_id}")
async def read_item(
        item_id: str
):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item": items[item_id]}
```

**添加自定义标题**

在某些情况下，能够向 HTTP 错误添加自定义标头很有用。例如，对于某些类型的安全性。

您可能不需要直接在代码中使用它。

但是，如果您在高级场景中需要它，您可以添加自定义标头：

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"foo": "The Foo Wrestlers"}


@app.post("/items_header/{item_id}")
async def read_item_header(
        item_id: str
):
    if item_id not in items:
        raise HTTPException(
            status_code=404,
            detail="Item Not Found",
            headers={"X-Error": "There goes my error"}
        )
    return {"items": items[item_id]}
```

据我的理解就是在返回的头部信息中加一条信息，可以是解释，也可以是其他的啥。

**安装自定义异常处理程序**
自定义的异常返回，返回自定义的信息：

```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()


class UnicornException(Exception):
    def __init__(self, name: str):
        self.name = name


@app.exception_handler(UnicornException)
async def unicorn_exception_handler(
        request: Request,
        exc: UnicornException
):
    return JSONResponse(
        status_code=418,
        content={"message": f"Oops! {exc.name} did something . there goes a rainbow"}
    )

# @app.exception_handler(UnicornException)
# async def unicorn_exception_handler(request: Request, exc: UnicornException):
#     return JSONResponse(
#         status_code=418,
#         content={"message": f"Oops! {exc.name} did something. There goes a rainbow..."},
#     )


@app.get("/unicorn/{name}")
async def read_unicorn(
        name: str
):
    if name == "yolo":
        raise UnicornException(name=name)
    return {"unicorn_name": name}
```

覆盖异常部分跳过了

## 路径操作配置

**响应状态码**

```python
from fastapi import FastAPI, status
from pydantic import BaseModel
from typing import Optional, Set

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []


@app.post("/items/", response_model=Item, status_code=status.HTTP_201_CREATED)
async def create_item(item: Item):
    return item
```

该响应码将在响应中使用，并将添加到OpenAPI架构中

**标签**

```python
from fastapi import FastAPI
from typing import Optional, Set
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    tax: Optional[float] = None
    price: float
    tags: Set[str] = []


@app.post("/items/", response_model=Item, tags=["items"])
async def create_item(item: Item):
    return item


@app.get("/items/", tags=["items"])
async def read_items():
    return {"name": "foo", "price": 31}


@app.get("/users/", tags=["users"])
async def read_items():
    return [{"name": "foo", "price": 32}]
```



![image-20210711155631018](img/image-20210711155631018.png)

​	**总结和描述**

```python
@app.post("/items/",
          response_model=Item,
          tags=["items"],
          summary="summary是啥",
          description="这是啥描述")
async def create_item(item: Item):
    return item
```

![image-20210711155939772](img/image-20210711155939772.png)

**文档字符串描述**

```python
@app.get("/users/", tags=["users"])
async def read_items():
    """
    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """
    return [{"name": "foo", "price": 32}]
```

![image-20210711160110372](img/image-20210711160110372.png)

**响应说明**

```python
@app.get("/items/",
         tags=["items"],
         response_description="这是一个响应说明")
async def read_items():
    return {"name": "foo", "price": 31}
```

![image-20210711160258843](img/image-20210711160258843.png)

**弃用路径操作**

```python
@app.get("/deprecated/", deprecated=True)
async def deprecated():
    return {"hello wro": "do"}
```

如果您需要将*路径操作*标记为deprecated，但不删除它，请传递参数`deprecated`：

![image-20210711160532182](img/image-20210711160532182.png)

## JSON兼容编码器

```python
from fastapi import FastAPI
from typing import Optional
from pydantic import BaseModel
from datetime import datetime
from fastapi.encoders import jsonable_encoder

app = FastAPI()

fake_db = {}


class Item(BaseModel):
    title: str
    timestamp: datetime
    description: Optional[str] = None


@app.put("/item/{id}")
def update_item(id: str, item: Item):
    json_compatible_item_data = jsonable_encoder(item)
    fake_db[id] = json_compatible_item_data
    return fake_db.values()
```

假设您有一个`fake_db`仅接收 JSON 兼容数据的数据库。

例如，它不接收`datetime`对象，因为这些对象与 JSON 不兼容。

因此，必须将`datetime`对象转换为`str`包含[ISO 格式](https://en.wikipedia.org/wiki/ISO_8601)数据的对象。

同样，该数据库不会接收 Pydantic 模型（具有属性的对象），只会接收`dict`.

你可以用`jsonable_encoder`它。

## 更新

PUT用于接收应替换现有数据的数据。

```python
from typing import List, Optional
from fastapi import FastAPI
from pydantic import BaseModel
from fastapi.encoders import jsonable_encoder

app = FastAPI()


class Item(BaseModel):
    name: Optional[str] = None
    description: Optional[str] = None
    price: Optional[float] = None
    tax: float = 132.2
    tags: List[str] = []


items = {
    "foo": {"name": "Foo", "price": 50.2},
    "bar": {"name": "Bar", "description": "The bartenders", "price": 62, "tax": 20.2},
    "baz": {"name": "Baz", "description": None, "price": 50.2, "tax": 10.5, "tags": []},
}


@app.get("/items/{item_id}", response_model=Item)
async def read_item(
        item_id: str
):
    return items[item_id]


@app.put("/items/{item_id}")
async def update_item(
        item_id: str,
        item: Item
):
    update_item_encoded = jsonable_encoder(item)
    items[item_id] = update_item_encoded
    return items
```



**部分更新PATCH**

部分更新部分没有看懂



## 依赖关系

**依赖注入**
有一种办法可以声明需要工作和使用的东西的依赖

```python
from fastapi import FastAPI, Depends
from typing import Optional

app = FastAPI()


async def comm_parameters(
        q: Optional[str] = None,
        skip: int = 0,
        limit: int = 100,
):
    return {"q": q, "skip": skip, "limit": limit}


@app.get("/items/")
async def read_items(
        commons: dict = Depends(comm_parameters)
):
    return commons


@app.get("/users/")
async def read_users(
        commons: dict = Depends(comm_parameters)
):
    return commons
```

依赖注入是： 声明一个函数，然后再查询参数里面注入这个函数

每当有新请求到达时，**FastAPI**都会处理：

- 使用正确的参数调用您的依赖项（“可靠”）函数。
- 从您的函数中获取结果。
- 将该结果分配给*路径操作函数中*的参数。

![image-20210712112502763](img/image-20210712112502763.png) 

这样您只需编写一次共享代码，**FastAPI**就会为您的*路径操作*调用它。

==这样子感觉这个依赖注入和模型主体注入没有多大的区别==

依赖项（可靠函数）可以使用`async def`或正常`def`。

==区别：模型主体注入的时候是一个整的json注入，然后这个依赖项注入是跟查询参数差不多的注入方式==

这些函数由框架（fastapi）调用

**fastapi插件**

可以使用**依赖注入**系统构建集成和“插件” 。但实际上，实际上**没有必要创建“插件”**，因为通过使用依赖**项**，可以声明无限数量的集成和交互，这些集成和交互可用于您的*路径操作功能*。

并且可以以非常简单和直观的方式创建依赖项，允许您只导入所需的 Python 包，并在几行代码中将它们与您的 API 函数集成，*字面意思是*.

您将在下一章中看到有关关系和 NoSQL 数据库、安全性等方面的示例。

**faspapi兼容性**

依赖注入系统的简单性使得**FastAPI**兼容：

- 所有关系数据库
- NoSQL 数据库
- 外部包
- 外部 API
- 认证和授权系统
- API 使用监控系统
- 响应数据注入系统
- 等等。

### 作为依赖的类

声明为函数的依赖项不是声明依赖项的唯一方法（尽管它可能更常见）

作为依赖项的类：

```python
from typing import Optional

from fastapi import Depends, FastAPI

app = FastAPI()


fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit


@app.get("/items/")
async def read_items(commons: CommonQueryParams = Depends(CommonQueryParams)):
    response = {}
    if commons.q:
        response.update({"q": commons.q})
    items = fake_items_db[commons.skip : commons.skip + commons.limit]
    response.update({"items": items})
    return response
```

函数或类作为依赖项的情况下，数据都将被转换、验证、记录在 OpenAPI 模式等上。

**类型注释与Depends**

```python
commons: CommonQueryParams = Depends(CommonQueryParams)
```

FastAPI 将从中提取声明的参数，这就是 FastAPI 将实际调用的内容。

在这种情况下，第一个`CommonQueryParams`, 在：

```python
commons: CommonQueryParams ...
```

...对**FastAPI**没有任何特殊含义。FastAPI 不会将它用于数据转换、验证等（因为它正在`= Depends(CommonQueryParams)`为此使用 ）。

你实际上可以只写：

```python
commons = Depends(CommonQueryParams)
```

例如：

```python
from typing import Optional

from fastapi import Depends, FastAPI

app = FastAPI()


fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit


@app.get("/items/")
async def read_items(commons=Depends(CommonQueryParams)):
    response = {}
    if commons.q:
        response.update({"q": commons.q})
    items = fake_items_db[commons.skip : commons.skip + commons.limit]
    response.update({"items": items})
    return response
```



**另一种表达方法**
依赖项*特别*是一个类，**FastAPI**将“调用”以创建类本身的实例。

对于这些特定情况，您可以执行以下操作：

而不是写：

```python
commons: CommonQueryParams = Depends(CommonQueryParams)
```

...你写：

```python
commons: CommonQueryParams = Depends()
```

你声明依赖作为参数的类型，并使用`Depends()`其“默认”值（即后`=`），该函数的参数，在没有任何参数`Depends()`，而不必编写完整的类*再次*里面的`Depends(CommonQueryParams)`。

例如：

```python
from typing import Optional

from fastapi import Depends, FastAPI

app = FastAPI()


fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit


@app.get("/items/")
async def read_items(commons: CommonQueryParams = Depends()):
    response = {}
    if commons.q:
        response.update({"q": commons.q})
    items = fake_items_db[commons.skip : commons.skip + commons.limit]
    response.update({"items": items})
    return response
```

### 子依赖

创建一个依赖：

```python
from fastapi import FastAPI, Depends, Cookie
from typing import Optional

app = FastAPI()


def query_extractor(q: Optional[str] = None):
    return q


def query_or_cookie_extractor(
        q: str = Depends(query_extractor),
        last_query: Optional[str] = Cookie(None)
):
    if not q:
        return last_query
    return q


@app.get("/items/")
async def read_query(
        query_or_default: str = Depends(query_or_cookie_extractor)
):
    return {"q_or_cookie": query_or_default}
```

![image-20210713094616079](img/image-20210713094616079.png) 

**多次使用相同的依赖项**

如果您的一个依赖项为同一个*路径操作*多次声明，例如，多个依赖项有一个共同的子依赖项，**FastAPI**将知道每个请求只调用该子依赖项一次。

它会将返回的值保存在“缓存”中，并将其传递给该特定请求中需要它的所有“依赖项”，而不是为同一请求多次调用依赖项。

在您知道需要在同一请求中的每一步（可能多次）调用依赖项而不是使用“缓存”值的高级场景中，您可以`use_cache=False`在使用时设置参数`Depends`：

```python
async def needy_dependency(fresh_value: str = Depends(get_value, use_cache=False)):

    return {"fresh_value": fresh_value}

```

### 路径操作装饰器中的依赖关系

**添加dependencies到路径操作装置**

```python
from fastapi import FastAPI, Header, HTTPException, Depends

app = FastAPI()


async def verify_token(x_token: str = Header(...)):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")


async def verify_key(x_key: str = Header(...)):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="X-Key header invalid")


@app.get("/items/", dependencies=[Depends(verify_key), Depends(verify_token)])
async def read_items():
    return [{"item": "foo"}, {"item": "bar"}]
```

我觉的这个请求执行的时候就会调用那两个依赖项（可靠函数），然后就有那个值的输入，以及判断和异常抛出

## 全局依赖

```python
from fastapi import FastAPI, Header, HTTPException,Depends


async def verify_token(x_token: str = Header(...)):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="x_token not fount")


async def verify_key(x_key: str = Header(...)):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="x_key not found")


app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])


@app.get("/items/")
async def read_items():
    return [{"item": "Portal Gun"}, {"item": "Plumbus"}]


@app.get("/users/")
async def read_users():
    return [{"username": "tom"}, {"password": "wefjsoef"}]
```

设置全局依赖：

```python
app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])
```

### 数据库依赖项

还没看，跳过了，看完数据库再看这个

## 安全

**password flow**
密码流是OAuth2中定义得流中得一种方式，去处理安全以及身份验证。
OAuth2被设计来已确保后端或api能够独立于授权用户得服务。
但是这个例子中fastpi程序将会处理api以及授权。
因此，让我们从简化的角度进行回顾：

- 用户在前端键入他username和和password，然后点击Enter。
- 前端（在用户的浏览器中运行）发送一个username和password我们的API在一个特定的URL。
- API会检查username和password，并以“令牌”作为响应
  - 令牌”只是一个包含一些内容的字符串，我们稍后可以使用它来验证该用户。
  - 通常，令牌设置为在一段时间后过期。
    - 因此，用户稍后将不得不再次登录。
    - 而且，如果令牌被盗，则风险会降低。它不像将永远有效的永久密钥（在大多数情况下）。
- 前端将该令牌临时存储在某个地方。
- 用户单击前端以转到前端Web应用程序的另一部分。
- 前端需要从API中获取更多数据。
  - 但是它需要针对该特定端点的身份验证。
  - 因此，为了通过我们的API进行身份验证，它会发送一个标头Authorization，其值Bearer加上令牌。
  - 如果令牌包含foobar，则Authorization标头的内容为：Bearer foobar。

**OAuth2PasswordBearer**
OAuth2PasswordBearer是fastapi提供得使用Bearer token得password流。
它是客户端使用username 和password来发送并获得token得类。
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/token")
它并不创建端点路径操作，但是声明客户端得到token得地址。
然后使用实例作为路径操作得依赖，这个依赖返回得参数是一个str类型得token。

它将会去请求得头部中查找Authorization，验证值是否是Bearer+token并且返回token。
如果头部中没有Authorization,或者值不包括Bearer token，将会返回401.
甚至不用你去检查token是否存在，你可以很确定只要函数被执行了，就能够得到token。

![image-20210717190445642](img/image-20210717190445642.png)

### **FastAPI**的OAuth2PasswordBearer

使用**OAuth2**和**密码**流，使用不**记名**令牌。我们使用`OAuth2PasswordBearer`类来做到这一点。

```python
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/items/")
async def read_items(token: str = Depends(oauth2_scheme)):
    return {"token": token}
```

获取当前用户：

```python
from fastapi import FastAPI, Depends
from typing import Optional
from pydantic import BaseModel
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[str] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded",
        email="john@example.com",
        full_name="johm"
    )


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return user


@app.get("/user/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user
```

那个

```python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")
```

为什么等于token

```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

fake_users_db = {
    "jnhndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "fakehashedsecret",
        "disabled": False
    },
    "alice": {
        "username": "alice",
        "full_name": "Alice Wonderson",
        "email": "alice@example.com",
        "hashed_password": "fakehashedsecret2",
        "disabled": True
    }
}


def fake_hash_password(password: str):
    return "fakehashed" + password


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


class UserInDb(User):
    hashed_password: str


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDb(**user_dict)


def fake_decode_token(token):
    # This doesn't provide any security at all
    # Check the next version
    user = get_user(fake_users_db, token)
    return user


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="token have problem",
            headers={"WWW-Authenticate": "Bearer"}
        )
    return user


async def get_current_active_user(current_user: User = Depends(get_current_user)):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="这是最后一个函数抛出的异常")
    return current_user


@app.post("/token")
async def login(
        form_data: OAuth2PasswordRequestForm = Depends()
):
    user_dict = fake_users_db.get(form_data.username)
    if not user_dict:
        raise HTTPException(status_code=400, detail="这是post_token第一个")
    user = UserInDb(**user_dict)
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="这是post_token第二个")
    return {"access_token": user.username, "token_type": "bearer"}


@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_active_user)):
    return current_user
```

这个里面的post方法，把用户名和密码的输入用表单表示出来了

然后get方法的话，我一直没有解开那个锁

### JWT和TOken

这两个东西我没有仔细看要用时候看

`utcnow()`：读取的时间一直都是系统的“世界标准时间”，不管系统的本地时区是否设置，读取的时间不会随这些设置变化；

## 中间件

原文说：’“中间件”是一个函数，它在被任何特定*路径操作*处理之前处理每个**请求**。以及在返回之前的每个**响应**。‘

跟java里面的aop很像哈

**创建中间件**

```python
import time

from fastapi import FastAPI, Request

app = FastAPI()


@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response
```

这个没看懂，不知道怎么用

## CORS（Cross-Origin资源共享）

**Origin**

Origin是协议 ( `http`, `https`)、域 ( `myapp.com`, `localhost`, `localhost.tiangolo.com`) 和端口 ( `80`, `443`, `8080`) 的组合。

所以这些都是不同的Origin

- `http://localhost`
- `https://localhost`
- `http://localhost:8080`

即使它们都在`localhost`，它们使用不同的协议或端口，因此，它们是不同的“起源”。

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    "http://localhost.tiangolo.com",
    "http://localhost.tiangolo.com",
    "http://localhost",
    "http://localhost:8080"
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"]
)


@app.get("/")
async def main():
    return {"message": "hello World"}
```

跨域问题的解决，具体的它中文翻译的莫名奇妙没有细琢磨，遇到再去看看



## SQL（关系数据库）

这里看到的是一个SQLALchemy的示例

可以轻松的将其调整为SQLALchemy支持的任何数据库，例如：

- PostgreSQL
- MySQL
- SQLite
- Oracle
- Microsoft SQL Server, etc.

在此示例中，我们将使用SQLite，因为它使用单个文件并且Python已集成支持，

稍后对于生产应用程序，您可能希望使用像PostgreSQL这样的数据库服务器。

### ORM

FASTAPI可与任何数据库和任何样式的库配合用于数据库通信。

一个常见的模式就是“ORM”:一个“对象关系映射库”

ORM具有在代码和数据库表（“关系”）中的对象之间进行转换（“映射”）的工具

使用ORM，通常会创建一个表示SQL数据库中的表的类，该类的每个属性都表示一个列，具有名称和类型。

并且该类的每个实例代表数据库中的一行。

这些ORM还具有在表和实体之间建立连接或关系的工具。

常见的ORM有例如：Django-ORM（Django框架的一部分）、SQLAlchemy ORM（SQLAlchemy的一部分，独立于框架）和Peewee（独立于框架）等。

在这里，我们将看到如何使用**SQLAlchemy ORM**。

以类似的方式，您可以使用任何其他 ORM。

**文件结构**
.
└── sql_app
    ├── __init__.py
    ├── crud.py
    ├── database.py
    ├── main.py
    ├── models.py
    └── schemas.py

该文件`__init__.py`只是一个空文件，但它告诉 Python，`sql_app`它的所有模块（Python 文件）都是一个包。

### **创建 SQLAlchemy 部件**

参考文件`sql_app/database.py`。

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite: ///./sql_app.db"
# SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

- 第一步：弄一个连接到数据库的url

- 第二步：创建一个SQLALchemy引擎engine
- connect_args={"check_same_thread": False}仅用于`SQLite`. 其他数据库不需要它。和线程相关，
- `SessionLocal`该类的每个实例都是一个数据库会话。该类本身还不是数据库会话。但是一旦我们创建了一个`SessionLocal`类的实例，这个实例就会成为实际的数据库会话。我们命名它`SessionLocal`以区别于我们从 SQLAlchemy 导入的Session`。
- 要创建`SessionLocal`类，使用函数`sessionmaker`：
- 使用`declarative_base()`返回一个类的函数。稍后我们将从这个类继承来创建每个数据库模型或类（ORM 模型）。

### **创建数据库模型**

Base从database（database.py上面的文件）导入

创建从它继承的类

```python
from sqlalchemy import Boolean, Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship

from .database import Base


class Item(Base):
    __tablename__ = "items"
    
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, index=True)
    owner_id = Column(Integer, ForeignKey("user.id"))
    
    owner = relationship("user", back_populates="items")


class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)
    
    items = relationship("Item", back_populates="owner")
```

- 该`__tablename__`属性告诉 SQLAlchemy 在数据库中为这些模型中的每一个使用的表的名称。
- 这些就是一个一个的类，对应数据库中的一个一个的表
- 这些属性中的每一个都代表其相应数据库表中的一列。
- relationship这个是用来创建关系的。但是不知道这个关系怎么用
- 还有每个属性中的index不知道代表什么意思

### **创建pydantic模型**

创建一个`ItemBase`和`UserBase`Pydantic*模型*（或者说“模式”）以在创建或读取数据时具有共同的属性。

并创建一个继承自它们的`ItemCreate`and `UserCreate`（因此它们将具有相同的属性），以及创建所需的任何其他数据（属性）。

因此，用户`password`在创建它时也会有一个。

但是为了安全起见，`password`其他 Pydantic*模型中*不会出现，例如在读取用户时不会从 API 发送。

```python
from pydantic import BaseModel
from typing import Optional, List


class ItemBase(BaseModel):
    title: str
    description: Optional[str] = None


class ItemCreate(ItemBase):
    pass


class Item(ItemBase):
    id: int
    owner_id: int

    class Config:
        orm_mode = True


class UserBase(BaseModel):
    email: str


class UserCreate(UserBase):
    password: str


class User(UserBase):
    id: int
    is_active: bool
    items: List[Item] = []

    class Config:
        orm_mode = True
```

创建的时候不知道id但是在查找的时候知道id

现在，在Pydantic*模型*读取，`Item`并且`User`，添加一个内部`Config`类。

此类[`Config`](https://pydantic-docs.helpmanual.io/#config)用于向 Pydantic 提供配置。

在`Config`类中，设置属性`orm_mode = True`。

Pydantic`orm_mode`会告诉 Pydantic*模型*读取数据，即使它不是`dict`，而是 ORM 模型（或任何其他具有属性的任意对象）。

这样，而不是仅仅尝试`id`从 a获取值`dict`，如下所示：

```python
id = data["id"]
```

它还会尝试从属性中获取它，例如：

```python
id = data.id
```

有了这个，Pydantic*模型*与 ORM 兼容，您只需`response_model`在*路径操作*的参数中声明它。

您将能够返回一个数据库模型，它会从中读取数据。

**ORM模式的技术细节**

SQLAlchemy 和许多其他的默认情况下是“延迟加载”。

这意味着，例如，除非您尝试访问包含该数据的属性，否则它们不会从数据库中获取关系数据。

例如，访问属性`items`：

```python
current_user.items
```

将使 SQLAlchemy 转到该`items`表并获取该用户的项目，但不是之前。

没有`orm_mode`，如果您从*路径操作*返回 SQLAlchemy 模型，它将不包括关系数据。

即使您在 Pydantic 模型中声明了这些关系。

但是在 ORM 模式下，由于 Pydantic 本身会尝试从属性（而不是假设为`dict`）访问它需要的数据，您可以声明要返回的特定数据，它甚至可以从 ORM 中获取它。

**CRUD工具**
现在让我们看看文件`sql_app/crud.py`。

在这个文件中，我们将有可重用的函数来与数据库中的数据进行交互。

**CRUD**来源于：**Ç** reate，**- [R** EAD，**ù** PDATE，和**d** elete。

...虽然在这个例子中我们只是创建和阅读。

**读取数据**

`Session`从导入`sqlalchemy.orm`，这将允许您声明`db`参数的类型，并在您的函数中进行更好的类型检查和完成。

导入`models`（SQLAlchemy 模型）和`schemas`（Pydantic*模型*/模式）。

创建实用函数以：

- 通过 ID 和电子邮件读取单个用户。
- 读取多个用户。
- 阅读多个项目。

```python
from sqlalchemy.orm import Session
from . import models, schemas


def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


def get_user_by_email(db: Session, email: str):
    return db.query(models.User).filter(models.User.email == email).first()


def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()


def create(db: Session, user: schemas.UserCreate):
    fake_hashed_password = user.password + "notreallyhashed"
    db_user = models.User(email=user.email, hashed_password=fake_hashed_password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user


def get_item(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.Item).offset(skip).limit(limit).all()


def create_user_item(db: Session, item: schemas.ItemCreate, user_id: int):
    db_item = models.Item(**item.dict(), owner_id=user_id)
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item
```

db也就是session是个神奇的东西，把增删改查都封装好了把，只要用就好了，最后那个add就是增加，commit就是提交

现在创建实用函数来创建数据。

步骤是：

- 使用您的数据创建 SQLAlchemy 模型*实例*。
- `add` 该实例对象到您的数据库会话。
- `commit` 对数据库的更改（以便保存）。
- `refresh` 您的实例（以便它包含来自数据库的任何新数据，例如生成的 ID）。

**mian FastAPI应用程序**

后面的没有仔细敲代码了，到时候实际开发可以再去看看，就是把学的东西整合起来，然后在加一些要注意的细节

## SQLAIchemy

### ORM 与 SQLAlchemy 简介

ORM 全称 `Object Relational Mapping`, 翻译过来叫`对象关系映射`。简单的说，ORM将数据库中的表与面向对象语言中的类建立了一种对应关系，这样，我们要操作数据库，数据库中的表或者表中的一条记录就可以直接通过操作类或者类实例来完成。

SQLALchemy是Python社区最知名的ORM工具之一，为高效和高性能的数据库访问设计，实现了完整的企业级持久模型。

## workbench

- 当勾选 PK 复选框时，该列就是数据表的主键

- 控制 UQ 列的勾选情况，即可操作数据表的唯一约束

- 在 Default/Expression 列中编辑字段的默认值

- 控制 NN 列的勾选情况，即可操作数据表的非空约束

- PK: primary key (column is part of a pk) 主键

   NN: not null (column is nullable) 是否为空

  -UQ: unique (column is part of a unique key)  外键

   AI: auto increment (the column is auto incremented when rows are inserted)  自动增加

   BIN: binary (if dt is a blob or similar, this indicates that is binary data, rather than text)  二进制

   UN: unsigned (for integer types, see docs: “10.2. Numeric Types”)

  \- ZF: zero fill (rather a display related flag, see docs: “10.2. Numeric Types”)
