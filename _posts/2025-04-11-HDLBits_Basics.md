---
layout: post
title: "[Verilog] Basics"
category: education
---
시험공부용으로 HDLBits의 문제 내용과 답안, 학습 포인트를 정리한 것.\
텍스트로 써서 공부하다가 지피티한테 던져주면 알아서 마크다운 문서로 뽑아준다.\
이런거 보면 이제는 공부한거 블로그나 깃헙에 올려놓고 나름 스펙이라고 주장하는 건 웃긴 일일듯\
아무튼 AI가 만든 예쁜 정리본을 감상하세요.

---
# 📘 Basics
---

## ✅ Simple wire

```verilog
module top_module (
    input in,
    output out
);
    assign out = in;
endmodule
```

📌 **포인트**  
- `assign` 문은 **combinational logic**을 표현할 때 사용됨  

---

## ✅ Four wires

```verilog
module top_module (
    input a, b, c,
    output w, x, y, z
);
    assign w = a;
    assign x = b;
    assign y = b;
    assign z = c;
    // assign {w, x, y, z} = {a, b, b, c};  // 이렇게도 가능함
endmodule
```

📌 **포인트**  
- `assign`으로 여러 출력을 각 입력에 연결  
- 중복된 입력(`b`)도 여러 출력에 연결 가능  
- `{}`는 **벡터 또는 여러 신호를 합치는 표현 (concatenation)**

---

## ✅ Inverter

```verilog
module top_module (
    input in,
    output out
);
    assign out = ~in;
endmodule
```

📌 **포인트**  
- `~`는 **비트 반전 연산자 (bitwise NOT)**  
- `assign`은 항상 **조합 논리에서 사용**  
- `~`는 단일 비트 입력뿐 아니라 벡터에도 사용 가능

---

## ✅ AND gate

```verilog
module top_module (
    input a, b,
    output out
);
    assign out = a & b;
endmodule
```

📌 **포인트**  
- `&`는 bitwise AND  
- 1비트 입력 두 개를 AND한 결과를 출력  

---

## ✅ NOR gate

```verilog
module top_module (
    input a, b,
    output out
);
    assign out = ~(a | b);
endmodule
```

📌 **포인트**  
- `|`는 bitwise OR, `~`로 NOR 표현  
- Verilog에서 **게이트 연산은 모두 bitwise로 표현**됨  
- `~(a | b)` 구조는 **논리 회로에서 NOR 게이트의 전형적인 표현**

---

## ✅ XNOR gate

```verilog
module top_module (
    input a, b,
    output out
);
    assign out = ~(a ^ b);
endmodule
```

📌 **포인트**  
- `^`는 XOR, `~(a ^ b)`는 XNOR 표현  
- Verilog는 **XNOR 연산자를 별도로 제공하지 않음**, 따라서 이렇게 구현  

---

## ✅ Declaring wires

```verilog
module top_module (
    input a, b, c, d,
    output out, out_n
);
    wire and_ab, and_cd;

    assign and_ab = a & b;
    assign and_cd = c & d;
    assign out = and_ab | and_cd;
    assign out_n = ~out;
endmodule
```

📌 **포인트**  
- 연산 중간 값을 저장하려면 `wire`로 선언  

---

## ✅ 7458 chip

```verilog
module top_module (
    input p1a, p1b, p1c, p1d, p1e, p1f,
    output p1y,
    input p2a, p2b, p2c, p2d,
    output p2y
);
    wire and1_p1, and2_p1;
    wire and1_p2, and2_p2;

    assign and1_p1 = p1a & p1b & p1c;
    assign and2_p1 = p1d & p1e & p1f;
    assign p1y = and1_p1 | and2_p1;

    assign and1_p2 = p2a & p2b;
    assign and2_p2 = p2c & p2d;
    assign p2y = and1_p2 | and2_p2;
endmodule
```

📌 **포인트**  
- 실제 논리 게이트 칩의 구조를 모사한 문제  
- 여러 입력을 하나의 AND로 묶고, 그 결과를 OR로 결합  
- 신호가 많아질수록 중간 wire를 선언해 모듈화하는 습관이 중요함
