## ZOO 파일 해석 

*각 코드는 namespace를 통해 참조가 가능케하며 여러 Program.cs가 있는데 이걸 구분하게 해주는 용도 (즉, 이름 충돌을 해결해준다.) 
ex) Implementation , Proxy는 각각 다른 네임스페이스이며 둘 모두 Program.cs를 가지고 있다.

# 1. Animal.cs
요약 
1. 해당 동물의 식별번호, 이름, 종 등을 불변하게 받는다.(readonly를 활용하여 읽기만 가능한 정보로 할당)
2. 단, 다른 코드에서 사용할 시 외부에서 읽혀야 하므로 새로운 변수를 통해 할당해놓는다.
3. 또한 override를 사용하여 해당 값을 객체(주소)가 아닌 내부 코드를 통해 비교 방식을 정하여 다른 객체일지라도 같은 동물로 판명되면 true 반환
  
```C#
using System;

namespace Zoo // namespace 선언
{
    public class Animal
    {
        // readme를 통해 읽기전용 변수 생성 
        private readonly string _id; 
        private readonly string _name;
        private readonly Species _species;
        // animal 정의 
        public Animal(string id, string name, Species species)
        {
            _id = id;
            _name = name;
            _species = species;
        }
        // 외부에서 읽을 수 있게 할당 
        public string Id => _id;

        public string Name => _name;

        public Species Species => _species;
        // 객체(주소값)이 아닌 새로운 규칙에 따라 동일한지 판단 
        public override bool Equals(object? obj)
        {
            // 완전히 같은 객체일 경우, true
            if (ReferenceEquals(this, obj))
            {
                return true;
            }
            // 애초에 animal클래스가 아닐 경우 false
            if (obj is not Animal other)
            {
                return false;
            }
            // Id, Name, Species등이 모두 같을 경우 true ( && 을 통해 동일 조건일 경우 를 걸었고 조건문이므로 true false중에 나온다. )
            return Id == other.Id
                && Name == other.Name
                && Species == other.Species;
        }
        // HashCode를 만들어 비교 
        public override int GetHashCode()
        {
            return HashCode.Combine(Id, Name, Species);
        }
    }
}
```

# 2. AnimalCollection
요약
1. animal클래스의 값을 리스트로 받아 저장하는 역할 ( 컨텐이너 역할 )
2. 추가, 삭제, 검색, 중복 검사 등 기능이 포함  

```C#
using System.Collections.Generic;

namespace Zoo
{
    public class AnimalCollection()
    {
        private readonly List<Animal> _animals = [];

        public bool AddAnimal(Animal animal)
        {
            if (_animals.Contains(animal))
            {
                return false;
            }
            _animals.Add(animal);
            return true;
        }

        public int AddAnimals(IEnumerable<Animal> animals)
        {
            int count = 0;
            foreach (Animal animal in animals)
            {
                if (AddAnimal(animal))
                {
                    count += 1;
                }
            }
            return count;
        }

        public bool RemoveAnimal(Animal animal)
        {
            return _animals.Remove(animal);
        }

        public IEnumerable<Animal> FindAllAnimals()
        {
            return [.. _animals];
        }

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
    }
}
```
        
        
