#개발자를 위한 D3.js 입문

##Intro
http://alignedleft.com/tutorials/d3 의 글을 기본으로 주석을 추가함


##fundamentals
###SVG

D3는 Scalable Vector Graphics. SVG를 사용해서 렌더링 합니다. SVG는 텍스트 기반 이미지 포멧으로 다음 예제를 보면 쉽게 이해할 수 있습니다.
```
<svg width="50" height="50>
    <circle cx="25" cy="25" r="22" fill="blue" stroke="gray" stroke-width="2" />
</svg>
```
실행해 보세요. 이건 이미지가 아닙니다.

svg는 최신 브라우저에서 대부분 지원하고 있습니다.

##setup

https://d3js.org/
```

<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>D3 Test</title>
      <script src="https://d3js.org/d3.v3.min.js" charset="utf-8"></script>
    </head>
    <body>
        <script type="text/javascript">
            // Your beautiful D3 code will go here
        </script>
    </body>
</html>     
```


##Adding elements
아래 코드를 실행해 봅시다.
```
d3.select("body").append("p").text("New paragraph!");
```
1. select 메소드를 통해 body 엘리먼트를 선택합니다. D3는 css selector 를 사용합니다.
2. p 엘리먼트를 생성하고 body 엘리먼트에 추가합니다.
3. 위의 p 엘리먼트에 text 엘리먼트를 세팅합니다.

jQuery에서 많이 보았듯이, D3는 메소드 체이닝을 사용하고 있습니다. 

체인 신텍스 없이 표현하면 아래와 같습니다.
```
var body = d3.select("body");
var p = body.append("p");
p.text("New paragraph!");
```

대부분의 D3메소드는 해당 엘리먼트를 랩핑한 D3객체인 selection을 리턴 합니다.
selection 객체는 아래와 같습니다.
기본적으로 groups 와 parents property가 있습니다.
parents는 부모 노드를 가지고 있으며 groups는 선택한 노드 집합을 가집니다. 
```
Selection {
   _groups,
   _parents
}
```
예를 들어 d3.select('body') 결과 아래와 같은 selection을 리턴합니다.
```
Selection {
    _groups: [
        body
    ],
    _parents: [
      html
    ]
}
```
##Binding data
```
var dataset = [ 5, 10, 15, 20, 25 ];

d3.select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("p")
    .text("New paragraph!");
```
먼저 body 앨리먼트를 가져오고 p 엘리먼트를 모두 선택한다? 어라 있지도 않은 p 엘리먼트를 가져온다구요? 지금 당장은 이해하기 어렵습니다. 일단 넘어가죠. 그다음 data() 메소드로 데이터를 바인딩합니다. 


좀더 자세히 보겠습니다.
d3.select('body') - body를 찾습니다.

.selectAll('p') - 위에 body를 부모로 하는 모든 p 엘리먼트를 찾습니다. 그런데 p 엘리먼트는 아직 존재하지 않는 엘리먼트 입니다. selectAll메소드는 찾는 엘리먼트가 없다면 빈 selection(empty selection) 을 리턴합니다. 

잠시 앞으로 다시 돌아가 보겠습니다.
selectAll() 메소드는 해당 엘리먼트를 랩핑한 selection 객체를 반환합니다.
```
<body>
   <br/><br/><br/>
```
br 엘리먼트 3개를  body 에 추가하고  아래 코드를 실행해 봅시다.
```
d3.selectAll('br')
```
```
Selection {
   _groups: [
       NodeList: [
         br,
            br,
            br
        ]
    ],
    ...
}
```
br 엘리먼트 3개를 가진 NodeList가 보입니다.
이번엔 empty selection을 봅시다.
```
d3.selectAll('foo')
```
```
Selection {
   _groups: [
      undefined
    ],
    ...
}
```
foo엘리먼트를 찾지 못했기 때문에 NodeList가 없습니다.

다시 data(dataset)메소드로 돌아오겠습니다.
```
for (; i < dataLength; ++i) {
  if (node = group[i]) {
    node.__data__ = data[i];
    update[i] = node;
  } else {
    enter[i] = new EnterNode(parent, data[i]);
  }
}
```
위의 d3.js 코드를 보면 dataset으로 받은 배열 요소 순서대로 처리합니다. 이때 data를 바인딩 할때 방금전에 설명한 groups의 nodelist를 순회하면서 존재하는 node라면 업데이트 하고 그렇지 않으면 EnterNode를 생성하도록 되어 있습니다.(우리가 지금 작성중인 예제는 empty selection 이기 때문에 위의 코드에서의 group은 빈배열 입니다.)

EnterNode가 생성된 결과는 아래와 같습니다.
```
selection {
    _enter: [
        [EnterNode { __data__: 5  }],
        [EnterNode { __data__: 10 }],
        [EnterNode { __data__: 15 }],
        [EnterNode { __data__: 20 }],
        [EnterNode { __data__: 25 }],        
    ],
    _groups: [
      [],[],[],[],[]
    ],
    ...
}
```

다음 enter()메소드를 통해 _enter를 _groups로 할당합니다.
```
Selection {
    _groups: [
        [EnterNode { __data__: 5  }],
        [EnterNode { __data__: 10 }],
        [EnterNode { __data__: 15 }],
        [EnterNode { __data__: 20 }],
        [EnterNode { __data__: 25 }],        
    ],
    _parents: [
      body
    ]
}
```
이제 _group 과 _parents 만 있는, 데이터가 바인딩된 기본 selection이 만들어 졌습니다. 앞에서 실습한대로 append() 메소드나 text() 메소드를 사용할 수 있습니다.

데이터 바인딩 전체 코드를 다시 보겠습니다.
```
var dataset = [ 5, 10, 15, 20, 25 ];
d3.select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("p")
    .text("New paragraph!");

```

존재하지 않는 p 엘리먼트를 찾는 selectAll('p') 메소드는 EnterNode를 생성하기 위한 빈 selection을 생성하기 위해 필요한 메소드 입니다.
결국 지금 예제에서 selectAll('p') 메소드에서 'p'는 의미없는 리터럴 입니다. selectAll('foo' ) 라고 해도 되고 selectAll() 이렇게 해도 똑같습니다.

참고
http://blog.nacyot.com/articles/2015-02-02-d3-selection/

##Using your data
text() 메소드를 아래와 같이 바꿔보겠습니다.
```
.text(function(d) { return d; });
```
바인딩한 데이터가 표시됩니다.

CSS 프로퍼티도 설정할 수 있습니다.
```
.style("color", "red");
```

custom function을 추가할 수 있습니다.
```
.style("color", function(d) {
    if (d > 15) {   //Threshold of 15
        return "red";
    } else {
        return "black";
    }
});
```

##Drawing divs
div로 만들어진 bar 차트를 만들어 보겠습니다.
먼저 스타일을 추가합니다.
```
div.bar {
    display: inline-block;
    width: 20px;
    height: 75px;   /* We'll override this later */
    background-color: teal;
}
```
attr()메소드를 통해 bar 클래스를 가진 div를 만듭니다.
```
var dataset = [ 5, 10, 15, 20, 25 ];

d3.select("body").selectAll("div")
    .data(dataset)
    .enter()
    .append("div")
    .attr("class", "bar");
```
bar 차트를 만들기 위해 div의 높이를 조정합니다.
```
.style("height", function(d) {
    var barHeight = d * 5;  //Scale up by factor of 5
    return barHeight + "px";
});
```
##An SVG primer
지금까지 D3를 이용해서 div 엘리먼트를 생성하고 조작하는법을 알아보았습니다. 그러나 native HTML을 다루는데 굳이 D3를 이용할 필요는 없겠죠. 이제 본론으로 들어가서 SVG를 다루어 보겠습니다.
canvas처럼 SVG 엘리먼트를 먼저 만들어야 합니다.
```
<svg width="500" height="50">
   something
</svg>
```

###Simple Shapes
svg 엘리먼트 안에 그려 넣을 visual element 를 넣으면 됩니다.
rect, circle, elipse, line, text, path가 있습니다.

먼저 사각형을 그려 봅시다.
```
<svg width="500" height="50">
   <rect x="0" y="0" width="500" height="50"/>
</svg>
```
원을 그려 봅시다.
cx, cy는 원의 센터 좌표값 입니다. r은 반지름.
```
<circle cx="250" cy="25" r="25"/>
```

타원도 원과 비슷하지만 rx, ry로 각각의 반지름을 정해야 합니다
```
<ellipse cx="250" cy="25" rx="100" ry="25"/>
```

라인은 시작점의 좌표와 끝점의 좌표가 필요합니다.
```
<line x1="0" y1="0" x2="500" y2="50" stroke="black"/>
```
텍스트에서 x는 글자의 왼쪽끝이며 y는 글자의 baseline virtical position입니다.
```
<text x="250" y="25">Easy-peasy</text>
```
텍스트는 css style도 입힐 수 있습니다.
```
<text x="250" y="25" font-family="sans-serif"
 font-size="25" fill="gray">Easy-peasy</text>
```
###Styling SVG Elements
SVG의 기본 스타일은 블랙, fill with no stroke 입니다. 다른 스타일을 입혀보도록 하죠.

공통 프로퍼티:
* fill 
 * named colors - orange
 * hex vlaues - #3388aa
 * RGB values - rgb(10, 150, 20)
 * RGB with alpha transparency - rgba(10, 150, 20, 0.5)

* stroke
* stroke-with
* opecity

text 프로퍼티:
* font-family
* font-size

먼저 inline 으로 스타일을 입히는 방법입니다.
```
<circle cx="25" cy="25" r="22" fill="yellow" stroke="orange" stroke-width="5"/>
```
클래스를 설정해서 스타일을 입히는 방법입니다.
```
.pumpkin {
    fill: yellow;
    stroke: orange;
    stroke-width: 5;
 }
<circle cx="25" cy="25" r="22" class="pumpkin"/>
```

###Layering and Drawing Order
SVG에는 layer 개념은 없습니다. CSS의 z-index를 지원하지 않습니다. 오직 x/y 평면으로만 정렬됩니다.
그래서 여러 도형을 그렸을때 겹치게 되는데 DOM 엘리먼트처럼 코드 순서대로 위로 겹쳐집니다.
```
<rect x="0" y="0" width="30" height="30" fill="purple"/>
<rect x="20" y="5" width="30" height="30" fill="blue"/>
<rect x="40" y="10" width="30" height="30" fill="green"/>
<rect x="60" y="15" width="30" height="30" fill="yellow"/>
<rect x="80" y="20" width="30" height="30" fill="red"/>
```

###Transparency
투명도를 조절할 수 있습니다.
```
<circle cx="25" cy="25" r="20" fill="rgba(128, 0, 128, 1.0)"/>
<circle cx="50" cy="25" r="20" fill="rgba(0, 0, 255, 0.75)"/>
<circle cx="75" cy="25" r="20" fill="rgba(0, 255, 0, 0.5)"/>
<circle cx="100" cy="25" r="20" fill="rgba(255, 255, 0, 0.25)"/>
<circle cx="125" cy="25" r="20" fill="rgba(255, 0, 0, 0.1)"/>
```
참고
http://alignedleft.com/tutorials/d3/an-svg-primer

##Drawing SVGs
이제 SVG 와 친숙해 졌으니 데이터를 이용해서 모형을 생성해 봅시다.

```
var svg = d3.select("body")
            .append("svg")
            .attr("width", 500)
            .attr("height", 50);
```

###Data-driven Shapes
```
var dataset = [ 5, 10, 15, 20, 25 ];
```
```
var w = 500;
var h = 50;
var dataset = [ 5, 10, 15, 20, 25 ];

var svg = d3.select("body")
            .append("svg")
            .attr("width", w)   // <-- Here
            .attr("height", h); // <-- and here!

```
```
var circles = svg.selectAll("circle")
                 .data(dataset)
                 .enter()
                 .append("circle");

circles.attr("cx", function(d, i) {
            return (i * 50) + 25;
        })
       .attr("cy", h/2)
       .attr("r", function(d) {
            return d;
       });
```
![](./images/data_driven_Shapes.png) 
    
```
.attr("fill", "yellow")
.attr("stroke", "orange")
.attr("stroke-width", function(d) {
    return d/2;
});
```
![](./images/data_driven_Shapes2.png) 


##Making a bar chart
bar 차트를 만들어 봅시다. 앞에서 div 엘리먼트로 만들었던 bar 차트 입니다. 
```
var dataset = [ 5, 10, 13, 19, 21, 25, 22, 18, 15, 13,
                11, 12, 15, 20, 18, 17, 16, 18, 23, 25 ];

d3.select("body").selectAll("div")
    .data(dataset)
    .enter()
    .append("div")
    .attr("class", "bar")
    .style("height", function(d) {
        var barHeight = d * 5;
        return barHeight + "px";
    });
```
![](./images/oldchart.png) 

이제 SVG로 만들어 봅시다.
다음에 계속...


한글 API 문서
https://github.com/zziuni/d3/wiki

