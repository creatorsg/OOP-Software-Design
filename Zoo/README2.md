## ZOO 파일 내 오버라이드 해석 

```C#
        public override bool Equals(object? obj)
        {
            if (ReferenceEquals(this, obj))
            {
                return true;
            }
            if (obj is not Animal other)
            {
                return false;
            }
            return Id == other.Id
                && Name == other.Name
                && Species == other.Species;
        }

        public override int GetHashCode()
        {
            return HashCode.Combine(Id, Name, Species);
        }
```

를 보면 원본 코드인 Equals랑은 다른 기준으로 구분하고 있다. 
System.Object 제공의 함수로는 Equals는 객체의 주소를 가져와 비교한다. 즉 같은 동물이라도 
animal1 = id1, name1, species1
animal2 = id1, name1, species1
이라면 다른 객체로 판단할 것이라는 것이다. 여기서 활용되는 것이 오버라이드이다. 
오버라이드란 흔히 자식 클래스로 재정의 하는 것이다. 라고 하는데 쉽게 말하자면 부모의 기능을 가져와 해당 기능을 수정하여 새로이 쓰거나 추가적 기능을 구현하여 좀 더 활용성을 높이는 것을 말한다.

즉, 여기선 System.Object의 Equals를 오버라이드 받아 새로운 기준으로 판단하도록 유도했다고 생각하면 되겠다. 
+ base.MethodName() 을 사용하면 부모메서드의 내용을 가져올 수 있다. 여기서 적용하자면

```C#
        public override bool Equals(object? obj)
        {
            if (base.Equals(obj)) return true; // Equals의 본래 기능대로 객체의 주소에 따라 할당

            if (ReferenceEquals(this, obj))
            {
                return true;
            }
            if (obj is not Animal other)
            {
                return false;
            }
            return Id == other.Id
                && Name == other.Name
                && Species == other.Species;
        }

        public override int GetHashCode()
        {
            return HashCode.Combine(Id, Name, Species);
        }
```

식으로 활용될 것이다. 

자, 그러면 여기서 우리는 오버라이드와 오버로드의 차이를 알아야 한다. 

```C#
        public Animal? FindAnimalById(string id)
        {
            foreach (Animal animal in _animals)
            {
                if (animal.Id == id)
                {
                    return animal;
                }
            }
            return null;
        }

        public IEnumerable<Animal> FindAnimalsBySpecies(Species species)
        {
            List<Animal> results = [];
            foreach (Animal animal in _animals)
            {
                if (animal.Species == species)
                {
                    results.Add(animal);
                }
            }
            return results;
        }

        public IEnumerable<Animal> FindAnimalsByName(string name)
        {
            List<Animal> results = [];
            foreach (Animal animal in _animals)
            {
                if (animal.Name == name)
                {
                    results.Add(animal);
                }
            }
            return results;
        }
```
은 현재 AnimalCollection.cs에 잇는 코드이다 이를 오버로드로 나타태면 

```C#
    public Animal? Find(string id)
    {
        foreach (Animal animal in _animals)
        {
            if (animal.Id == id)
                return animal;
        }
        return null;
    }

    public IEnumerable<Animal> Find(Species species)
    {
        List<Animal> results = [];
        foreach (Animal animal in _animals)
        {
            if (animal.Species == species)
                results.Add(animal);
        }
        return results;
    }

    public IEnumerable<Animal> FindByName(string name)
    {
        List<Animal> results = [];
        foreach (Animal animal in _animals)
        {
            if (animal.Name == name)
                results.Add(animal);
        }
        return results;
    }
```
처럼 구현할 수 있겠다. 
단, 이렇게 쓸 경우 각각의 기능에 대한 모호성이 생길 수 있다.
하지만 기능이 거의 같아면 (현재의 경우 : 탐색 ) 이라면 find만 기억한다면 언제든 가져다 사용할 수 있다.

오버로드 시 주의해야 할 점이 있다.
분별력이 떨어질 가능성이 존재하며, 각각의 매개변수의 식별자, 개수 등 차이가 있어야 한다. 
만약 이것들이 지켜지지 않을 경우 컴파일러 오류를 야기할 뿐이다. 
그렇다면 현재 내 코드를 다시 봐보자. 
- public IEnumerable<Animal> FindByName(string name)
- public Animal? Find(string id)
는 둘 다 string을 사용했으며 둘 다 인자가 1개이다. 이러할 때 해결법은 거의 완전히 동일한
- public IEnumerable<Animal> FindByName(string name)
- public IEnumerable<Animal> Find(Species species)
만 오버라이드 후
- public Animal? FindAnimalById(string id)
만 따로 사용해도 되나 그러면 구분이 명확해지지 않으니 원본 코드와 같이 분별력을 주며 이름으로 기능 유추가 되게 하는 것도 좋은 방법이다.
쉽게 정리하자면
## 오버로드가 항상 좋지만은 않을 수도 있으며 편리성을 제공하나 따져야 할 것이 많다.

## 요약 정리

# 오버라이딩 
1. 부모 메서드의 기능을 가져와 자식 메서드에서 재정의하여 기능을 활용하는 것

# 오버로드
1. 같은 이름의 메서드를 매개변수의 차이를 주어 여러 메서드를 정의하는 것

# 둘의 차이점 
1. 오버라이딩은 실제 어떤 객체를 가르키는지 봐야하기에 동적바인딩으로 설정되고 오버로드는 매개변수를 보고 컴파일 시 어떤 메서드를 쓸지 알게 되기에 정적바인딩으로 구현된다.
2. 오버라이딩의 매개변수는 동일해야 하지만 오버로드는 동일해서는 안 된다.
3. 오버라이딩은 같은 부모-자식 클래스 간의 호환되지만 오버로드는 동일 클래스 내에서만 가능하다.	
