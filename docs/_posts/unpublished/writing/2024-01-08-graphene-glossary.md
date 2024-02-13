---
layout: single
title: 
date: 2024-01-08 22:26
category: 
author: 
tags: []
summary: 
---

> https://docs.graphene-python.org/en/latest/quickstart/

## Scheme

`Schema`는 서버에서 API로 가져올 수 있는 데이터의 필드 타입과 필드간 관계를 정의한다.


스키마를 생성하려면 operation, query, mutation, subscription을 정의한 root object를 제공해야한다.

이 중 query는 필수다.

```python
my_schema = Schema(
    query=MyRootQuery,
    mutation=MyRootMutation,
    subscription=MyRootSubscription
)
```

root query는 API의 진입점이 되는 필드들을 정의하는 특별한 ObjectType이다. 하지만 일반 ObjectType과 구조적으로 다르지는 않다.

- 스키마의 Query는 서버에서 가져오는 데이터가 어떤 필드를 갖는지 정의한다.
- Mutation은 서버의 데이터를 수정하거나 수정을 복구한다.
- Subscription은 실시간으로 수정을 클라이언트에게 보낸다.


스키마는 root operation에서 사용하는 모든 타입 정의들을 모아서 `validator`와 `executor`에 제공한다.

### Querying

스키마를 쿼리하려면 execute 메소드를 호출한다.

```python
query_string = 'query whoIsMyBestFriend { myBestFriend { lastName } }'
my_schema.execute(query_string)
```

### Query

쿼리는 요청할 데이터 필드를 묘사해서 실제 데이터를 요청 하는 행위다.

쿼리는 `query string` 파라미터와 함께 스키마의 `execute` 메서드를 실행해서 동작한다.

```python
class Query(ObjectType):
    
    # 스키마 필드, first_name이라는 아규먼트를 가지며 기본값은 stranger
    hello = String(first_name=String(default_value="stranger"))
    # 스키마 필드
    goodbye = String()


    # 쿼리 요청을 받았을 때 필드 데이터가 어떻게 결정될 지를 정의한다.
    def resolve_hello(root, info, first_name):
        # hello는 파라미터를 받을 수 있기 때문에 아규먼트 first_name이 추가됨.
        return f'Hello {first_name}!'

    def resolve_goodbye(root, info):
        return 'See ya!'

# 스키마 생성
schema = Schema(query=Query)

# hello 필드를 묘사하는 쿼리 스트링 (아규먼트 미명시)
query_string = '{ hello }'

# 쿼리
result = schema.execute(query_string)
print(result.data['hello'])
# > "Hello stranger!"


# GraphQL 이란 값을 갖는 아규먼트 firstName과 함께 hello를 요청하는 쿼리를 전달
query_with_argument = '{ hello(firstName: "GraphQL") }'

# 쿼리 2
result = schema.execute(query_with_argument)
print(result.data['hello'])
# > "Hello GraphQL!"
```

## Scalars

> https://docs.graphene-python.org/en/latest/types/scalars/

스칼라 타입은 쿼리 말단의 구체적인 값을 나타낸다.

그래핀에는 파이썬의 기본 데이터 값 형식들을 위한 빌트인 타입들이 있다.

