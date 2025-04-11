---
layout: post
title: "[Verilog] Vectors"
category: education
---
---
# 📘 Vectors
---

## ✅ Vectors

```verilog
module top_module (
    input [2:0] vec,
    output [2:0] outv,
    output o2,
    output o1,
    output o0
);
    assign outv = vec;
    assign o0 = vec[0];
    assign o1 = vec[1];
    assign o2 = vec[2];
    // assign {o2, o1, o0} = vec;
endmodule
```

📌 **포인트**  
- `[2:0]`은 3비트 벡터이며, 각각의 비트에 개별 접근이 가능함  
- `assign outv = vec;`는 전체 벡터를 연결하고, 아래는 각각을 분해하여 출력하는 방식

---

## ✅ Vectors in more detail

```verilog
`default_nettype none
module top_module (
    input wire [15:0] in,
    output wire [7:0] out_hi,
    output wire [7:0] out_lo
);
    assign out_hi = in[15:8];
    assign out_lo = in[7:0];    
    // assign {out_hi, out_lo} = in;
endmodule
```

📌 **포인트**  
- `[15:8]`과 `[7:0]`를 이용해 상위 8비트, 하위 8비트를 나눌 수 있음  
- `default_nettype none`은 암시적 wire 선언을 금지함 (명시적 선언이 필요함)

---

## ✅ Vector part select

```verilog
module top_module (
    input [31:0] in,
    output [31:0] out
);
    assign out[31:24] = in[7:0];
    assign out[23:16] = in[15:8];
    assign out[15:8] = in[23:16];
    assign out[7:0] = in[31:24];
endmodule
```

📌 **포인트**  
- 벡터의 일부분을 선택하여 다른 위치에 복사할 수 있음  
- 이 문제에서는 32비트를 8비트 단위로 순서를 바꾸어 출력

---

## ✅ Bitwise operators

```verilog
module top_module (
    input [2:0] a,
    input [2:0] b,
    output [2:0] out_or_bitwise,
    output out_or_logical,
    output [5:0] out_not
);
    assign out_or_bitwise = a | b;   // bitwise OR
    assign out_or_logical = a || b;  // logical OR
    assign out_not[2:0] = ~a;
    assign out_not[5:3] = ~b;
endmodule
```

📌 **포인트**  
- `|`는 비트 단위 OR, `||`는 논리 OR (문법적으로는 벡터에 적합하지 않음)  
- 벡터를 NOT할 때는 각 비트를 반전하며, 조합 출력으로 사용 가능

---

## ✅ Four-input gates

```verilog
module top_module (
    input [3:0] in,
    output out_and,
    output out_or,
    output out_xor
);
    assign out_and = in[3] & in[2] & in[1] & in[0];
    assign out_or  = in[3] | in[2] | in[1] | in[0];
    assign out_xor = in[3] ^ in[2] ^ in[1] ^ in[0];
endmodule

```

📌 **포인트**  
- 각각의 4개 비트에 대해 AND, OR, XOR 연산을 수행함
- `&in`, `|in`, `^in`도 같은 방식으로 사용 가능 (reduction operator)

---

## ✅ Vector concatenation operator

```verilog
module top_module (
    input [4:0] a, b, c, d, e, f,
    output [7:0] w, x, y, z
);
    wire [31:0] concat;
    assign concat = {a, b, c, d, e, f, 2'b11};
    assign w = concat[31:24];
    assign x = concat[23:16];
    assign y = concat[15:8];
    assign z = concat[7:0];
endmodule
```

📌 **포인트**  
- 여러 벡터를 `{}`로 붙여서 하나의 큰 벡터 생성  
- 잘라서 출력할 때는 `[31:24]`, `[23:16]` 등으로 쪼갬

---

## ✅ Vector reversal 1

```verilog
module top_module (
    input [7:0] in,
    output [7:0] out
);
    assign out = {in[0], in[1], in[2], in[3], in[4], in[5], in[6], in[7]};
    
    // 이 회로는 벡터를 수동으로 반전하는 방법 외에도
    // generate-for 또는 always block 내 loop를 사용하여 구현할 수 있음.
    // 아래는 참고용 SystemVerilog 스타일의 예시 코드:
    /*always @(*) begin    
        for (int i = 0; i < 8; i++)
            out[i] = in[8 - i - 1];
    end*/

endmodule

```

📌 **포인트**  
- `assign out = {...}` 방식은 **벡터를 수동으로 반전**한 것
- 주석에 있는 `for` 루프는 SystemVerilog 스타일의 procedural block 방식

---

## ✅ Replication operator

{% raw %}
```verilog
module top_module (
    input [7:0] in,
    output [31:0] out
);
    assign out = {{24{in[7]}}, in[7:0]};
endmodule
```
{% endraw %}

📌 **포인트**  
- `{N{X}}` 문법은 X를 N번 반복하는 replication operator  
- 이 문제는 **sign extension (부호 확장)** 연습 문제
- Sign extension은 숫자의 부호를 유지하면서 비트 수를 늘리기 위해, 최상위 비트(부호 비트)를 반복하여 앞쪽을 채우는 방식

---

## ✅ More replication

{% raw %}
```verilog
module top_module (
    input a, b, c, d, e,
    output [24:0] out
);
    wire [24:0] top;
    wire [24:0] bottom;
    assign top = {{5{a}}, {5{b}}, {5{c}}, {5{d}}, {5{e}}};
    assign bottom = {5{a,b,c,d,e}};
    assign out = ~(top ^ bottom);
endmodule
```
{% endraw %}

📌 **포인트**  
- 중첩된 replication 사용  
- `5{a,b,c,d,e}`는 각 비트 묶음을 5번 반복  
- `~(top ^ bottom)`으로 두 벡터의 XNOR 계산
