---
layout: post
title: "[Verilog] More Verilog Features"
category: education
---
---

# 📘 More Verilog Features

---

## ✅ Conditional ternary operator

```verilog
module top_module (
    input [7:0] a, b, c, d,
    output [7:0] min);
    
    wire [7:0] min1;
    wire [7:0] min2;
    
    assign min1 = (a < b) ? a : b;
    assign min2 = (c < d) ? c : d;
    assign min = (min1 < min2) ? min1 : min2;

endmodule
```

📌 **포인트**
- `? :` 조건 연산자는 **if문 없이 한 줄로 MUX 구현**이 가능
- 여러 단계로 중첩하여 **4개 이상의 비교 로직도 처리 가능**

---

## ✅ Reduction operators

```verilog
module top_module (
    input [7:0] in,
    output parity); 
    
    assign parity = ^in;

endmodule
```

📌 **포인트**
- `&`, `|`, `^` 연산자를 하나의 벡터에 적용하면 **reduction 연산자**로 사용됨
- **XOR reduction**은 **parity bit 계산**에 활용됨
- **벡터 내부의 모든 비트** 각각에 대한 연산

---

## ✅ Reduction: Even wider gates

```verilog
module top_module( 
    input [99:0] in,
    output out_and,
    output out_or,
    output out_xor 
);
    
    assign out_and = &in;
    assign out_or = |in;
    assign out_xor = ^in;

endmodule
```

📌 **포인트**
- 100비트 이상의 **대규모 벡터 게이트 연산**도 reduction operator 하나로 구현
- **복잡한 회로 간결하게 표현** 가능

---

## ✅ Combinational for-loop: Vector reversal 2

```verilog
module top_module( 
    input [99:0] in,
    output [99:0] out
);
    
    always @(*) begin
        for (int i = 0; i < $bits(out); i++) begin
            out[i] = in[$bits(out) - i - 1];
        end
    end

endmodule
```

📌 **포인트**
- `always @(*)` 안의 `for`문은 **하드웨어 구조를 반복 생성하는 문법**
- `$bits(out)`는 out의 비트 수(100)를 계산하여 반복 조건에 사용됨
- `out[i] = in[99-i]`와 같음 → **벡터 뒤집기**

---

## ✅ Combinational for-loop: 255-bit population count

```verilog
module top_module( 
    input [254:0] in,
    output [7:0] out );
    
    always @(*) begin
        out = 0;
        for(int i = 0; i < $bits(in); i++) begin
            out = out + in[i];
        end
    end

endmodule
```

📌 **포인트**
- 전체 비트 중 **1의 개수(=population count)를 계산**
- 반복문 안에서 누적합을 더하려면 먼저 **out을 초기화해야 함**
- 누락 시 시뮬레이션과 합성 결과 불일치 가능 → **초기화 필수**

---

## ✅ Generate for-loop: 100-bit binary adder 2

```verilog
module full_adder (
    input a,
    input b,
    input cin,
    output sum,
    output cout
);
    assign sum = a ^ b ^ cin;
    assign cout = (a & b) | (a & cin) | (b & cin);
endmodule

module top_module( 
    input [99:0] a, b,
    input cin,
    output [99:0] cout,
    output [99:0] sum );
    
    wire [99:0] carry;
    
    genvar i;
    generate
        for (i = 0; i < 100; i++) begin: fa_chain
            if (i == 0) begin
                full_adder u_fa (
                    .a(a[0]),
                    .b(b[0]),
                    .cin(cin),
                    .sum(sum[0]),
                    .cout(carry[0])
                );
            end else begin
                full_adder u_fa (
                    .a(a[i]),
                    .b(b[i]),
                    .cin(carry[i - 1]),
                    .sum(sum[i]),
                    .cout(carry[i])
                );
            end
            assign cout[i] = carry[i];
        end
    endgenerate  

endmodule
```

📌 **포인트**
- `generate-for`는 **반복 구조를 컴파일 타임에 생성**
- `genvar`로 선언된 변수는 `generate`**안에서만 사용 가능**
- **carry 배열을 중간 신호로 따로 만들고**, 마지막에 `cout[i]`로 외부 출력

---

## ✅ Generate for-loop: 100-digit BCD adder

```verilog
/*
module bcd_fadd (
    input [3:0] a,
    input [3:0] b,
    input cin,
    output cout,
    output [3:0] sum
);
*/
module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );

    wire [99:0] carry;

    genvar i;
    generate
        for (i = 0; i < 100; i++) begin: bcd_adders
            if (i == 0) begin
                bcd_fadd u_bcd (
                    .a(a[3:0]),
                    .b(b[3:0]),
                    .cin(cin),
                    .cout(carry[0]),
                    .sum(sum[3:0])
                );
            end else begin
                bcd_fadd u_bcd (
                    .a(a[(4*i)+3 : 4*i]),
                    .b(b[(4*i)+3 : 4*i]),
                    .cin(carry[i-1]),
                    .cout(carry[i]),
                    .sum(sum[(4*i)+3 : 4*i])
                );
            end
        end
    endgenerate

    assign cout = carry[99];  

endmodule
```

📌 **포인트**
- BCD(Binary Coded Decimal)는 **4비트당 1자리 십진수(0~9)**를 표현 (자릿수 도입)
- `generate` 안에서 `i == 0`일 때만 `cin`을 연결하고, 이후는 이전 carry 받아옴
- **같은 인스턴스 이름(u_bcd)**도 `generate` 블록 안에서는 각각 다른 것으로 인식됨
- 범위 선택에서 `[(4*i)+3 : 4*i]` 대신 `[4*i +: 4]` 사용 가능

