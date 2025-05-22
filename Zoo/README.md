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

namespace Zoo 
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

#2. Species.cs
요약
1. 종에 할당되는 값에 대한 유효성 검사
2. override를 사용하여 해당 값을 객체(주소)가 아닌 내부 코드를 통해 비교 방식을 정하여 다른 객체일지라도 같은 동물로 판명되면 true 반환
```C#
using System;

namespace Zoo // namespace 선언 
{
    public class Species 
    {
        private readonly string _value; // 읽기 전용 문자열 _value 값 선언 

        public Species(string value) // value값 입력 
        {
            if (string.IsNullOrWhiteSpace(value)) // 만약 널공간 또는 빈칸이 있는 문자열일 경우 발동
            {
                throw new ArgumentException(
                    "Cannot accept an empty or white space string for a species name.", nameof(value));
            }
            _value = value;
        }

        public string Value => _value; // 다른 곳에서 접근할 수 있도록 단, 읽기만 됨 (수정 불가)

        public override bool Equals(object? obj) // value값에 의하여 동일한지 비교 
        {
            if (ReferenceEquals(this, obj))
            {
                return true;
            }
            if (obj is not Species other)
            {
                return false;
            }
            return Value.Equals(other.Value);
        }

        public override int GetHashCode() => Value.GetHashCode(); // 해쉬코드를 통한 비교 

        public override string ToString() 
        {
            return Value;
        }

        public static implicit operator string(Species species) => species.Value;

        public static implicit operator Species(string species) => new(species);

        public static bool operator ==(Species x, Species y)
        {
            return x.Equals(y);
        }

        public static bool operator !=(Species x, Species y)
        {
            return !x.Equals(y);
        }
    }
}
```

# 3. AnimalCollection.cs
요약
1. animal클래스의 값을 리스트로 받아 저장하는 역할 ( 컨텐이너 역할 )
2. 추가, 삭제, 검색, 중복 검사 등 기능이 포함  

```C#
using System.Collections.Generic;

namespace Zoo
{
    public class AnimalCollection() 
    {
        private readonly List<Animal> _animals = []; //Animal 클래스의 값들을 List의 형식으로 받음 ( readonly이기에 수정은 불가하지만 삭제 추가는 가능 )

        public bool AddAnimal(Animal animal)  // 리스트에 추가가 가능한지 불가능한지 확인하는 함수
        {
            if (_animals.Contains(animal)) // 만약 _animals에 Contains(리스트 제공 기본 함수)를 통해 동일 정보가 있다면 false 반환 
            {
                return false;
            }
            _animals.Add(animal); // false가 뜨지 않는다면 추가  ( 흐름제어에 의하여 false가 뜰 경우 도달하지 못 함 )
            return true;
        }

        public int AddAnimals(IEnumerable<Animal> animals) // IEnumerable이란, <식별자> 값을 나열하여 입력 ( 즉, 여러 동물이 들어갈 수 있다. )
        {
            int count = 0; // 들어가는 동물 수 체크용 
            foreach (Animal animal in animals) // foreach 를 통해 나열된 값들을 하나하나 추가 
            {
                if (AddAnimal(animal)) // 있는지 검증 후 추가 만약 false일 시 count는 오르지 않음 
                {
                    count += 1;
                }
            }
            return count; // 총 들어간 동물의 수 
        }

        public bool RemoveAnimal(Animal animal) // 인자에 들어간 동물 제거 
        {
            return _animals.Remove(animal); // 리스트 내에 값 삭제
        }

        public IEnumerable<Animal> FindAllAnimals() // 목록 전체 반환 ( 들어있는 동물들 확인용 )
        {
            return [.. _animals]; 
        }

        public Animal? FindAnimalById(string id) //nullable // id를 통해 원하는 동물 탐색  ( nullable (?) 를 통해 원하는 값이 없으면 null 출력 )
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

        public IEnumerable<Animal> FindAnimalsBySpecies(Species species) // 종에 따른 동물 탐색 
        {
            List<Animal> results = []; // id는 고유 번호지만 종은 겹칠 수 있기에 배열로 선언 
            foreach (Animal animal in _animals)
            {
                if (animal.Species == species)
                {
                    results.Add(animal);
                }
            }
            return results;
        }

        public IEnumerable<Animal> FindAnimalsByName(string name) // 이름에 따른 동물 탐색 
        {
            List<Animal> results = []; // id는 고유 번호지만 이름은 겹칠 수 있기에 배열로 선언 
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

# 4. Application.cs
요약
1. 출력 요소 및 사용자 입력 명령 등
2. 오류 코드 출력

using System;
using System.IO;
using System.Linq;

namespace Zoo
{
    public class Application(TextReader textReader)
    {
        private readonly AnimalCollection _animalCollection = new(); // 인스턴스화 

        public void Start()
        {
            PrintMenu();
            for (ApplicationInputResult inputResult = ReadValue(); 
                !inputResult.IsRequestTermination();
                PrintMenu(), inputResult = ReadValue())
            {
                string value = inputResult.Value.ToLower(); 
                if (value == "p")
                {
                    PrintAnimals(); // 동물 출력 
                }
                else if (value == "a")
                {
                    AddAnimals(); // 동물 추가 
                }
                else
                {
                    NotifyUnknownInput(); // 정해지지 않은 인풋 (아래 함수로 구현)
                }
            }
        }

        private ApplicationInputResult ReadValue()
        {
            string value = textReader.ReadLine()
                ?? throw new ApplicationException("입력을 받을 수 없습니다.");
            return new ApplicationInputResult(value);
        }

        private void PrintMenu()
        {
            Console.WriteLine("P: 동물 정보 출력하기");
            Console.WriteLine("A: 동물 정보 등록하기");
            Console.WriteLine("Q: 종료");
        }

        private void PrintAnimals()
        {
            int animalCount = _animalCollection.FindAllAnimals().Count();
            if (animalCount == 0)
            {
                Console.WriteLine("아직 등록된 동물이 없습니다.");
                return;
            }
            foreach (Animal animal in _animalCollection.FindAllAnimals())
            {
                Console.WriteLine($"| 이름: {animal.Name}\t\t| 종: {animal.Species}\t\t |");
            }
        }

        private void AddAnimals() // 동물 추가 로직 
        {
            Console.Write("동물의 이름을 입력해 주세요: ");
            string name = ReadValue();
            while (string.IsNullOrWhiteSpace(name)) // 이름 규칙 위반  검증 
            {
                Console.WriteLine("정확한 동물의 이름을 입력해 주세요.");
                Console.Write("동물의 이름을 입력해 주세요: ");
                name = ReadValue();
            }
            Console.Write("동물의 종을 입력해 주세요: ");
            string species = ReadValue();
            while (string.IsNullOrWhiteSpace(species))
            {
                Console.WriteLine("정확한 동물의 종을 입력해 주세요.");
                Console.Write("동물의 종을 입력해 주세요: ");
                species = ReadValue();
            }
            string id = Guid.NewGuid().ToString();
            Animal animal = new(id, name, species);
            if (_animalCollection.AddAnimal(animal))
            {
                Console.WriteLine("동물 정보가 등록되었습니다.");
            }
            else
            {
                Console.WriteLine("중복된 동물 정보가 이미 등록돼 있습니다.");
            }
        }

        private void NotifyUnknownInput() // 정해지지 않은 인풋
        {
            Console.WriteLine("해당하는 명령어를 찾을 수 없습니다.");
        }

        private class ApplicationInputResult(string value)
        {
            public string Value { get; } = value;

            public bool IsRequestTermination()
            {
                return Value.Equals("q", StringComparison.CurrentCultureIgnoreCase);
            }

            public static implicit operator string(ApplicationInputResult inputResult)
            {
                return inputResult.Value;
            }
        }
    }
}
        
        
