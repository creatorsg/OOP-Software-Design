## ZOO 파일 해석 

*각 코드는 namespace를 통해 참조가 가능케하며 여러 Program.cs가 있는데 이걸 구분하게 해주는 용도 (즉, 이름 충돌을 해결해준다.) 
ex) Implementation , Proxy는 각각 다른 네임스페이스이며 둘 모두 Program.cs를 가지고 있다.

# 1. Animal.cs
요약 
1. 해당 동물의 식별번호, 이름, 종 등을 불변하게 받는다.(readonly를 활용하여 읽기만 가능한 정보로 할당)
2. 단, 다른 코드에서 사용할 시 외부에서 읽혀야 하므로 String 값으로 읽어 새로이 할당해놓는다.
3. 또한 override를 사용하여 해당 값을 객체(주소)가 아닌 내부 코드를 통해 비교 방식을 정하여 다른 객체일지라도 같은 동물로 판명되면 true 반환
 ```C#  
        public Animal(string id, string name, Species species)      
        {
            _id = id;
            _name = name;
            _species = species;
        }
```
        각 인자를 받아 저장하는 방식으로 _를 사용하는 이유로는 

        만약, 코드를 
```        
        public Animal(string id, string name, Species species)
        {
            id = id;
            name = name;
            species = species;
        }
```
        로 할 경우, 그저 매개변수끼리 할당되므로 구분을 위해 
```        
        private readonly string _id;
        private readonly string _name;
        private readonly Species _species;
```
        로 정의후 _id를 사용한다 다른 해결 법으로는 .this는 있으나... _id가 더 편하지 않을까?
```
        public string Id => _id;
        public string Name => _name;
        public Species Species => _species;
```
        

        
        
        
