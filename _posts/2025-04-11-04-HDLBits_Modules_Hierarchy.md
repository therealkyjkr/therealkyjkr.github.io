---
layout: post
title: "[Verilog] Modules: Hierarchy"
category: education
---
---
# 📘 Modules: Hierarchy
---

## ✅ Modules

```verilog
/* (provided)
module mod_a(
    input in1,
    input in2,
    output out
);
endmodule
*/

module top_module(
    input a,
    input b,
    output out
);    
    mod_a inst1(
        .in1(a),
        .in2(b),
        .out(out)
    );
    // mod_a inst1(a, b, out);
endmodule
```

📌 **포인트**  
- 하위 모듈을 인스턴스화하여 상위 모듈에서 사용  
- `.포트명(신호명)` 형태의 **이름 기반 포트 연결** 사용

---

## ✅ Connecting ports by position

```verilog
module top_module(
    input a,
    input b,
    input c,
    input d,
    output out1,
    output out2
);
    mod_a inst1(out1, out2, a, b, c, d);
    // 문제의 mod_a가 숨겨져 있어서 이름 기반 연결 불가능
endmodule
```

📌 **포인트**  
- 포지션 기반 연결은 **포트 선언 순서에 맞게 신호를 연결**해야 함  
- 하위 모듈의 포트 정의가 숨겨져 있을 경우, 이름 기반 연결이 불가능하여 포지션 기반을 사용

---

## ✅ Connecting ports by name

```verilog
/*
module mod_a (
    output out1,
    output out2,
    input in1,
    input in2,
    input in3,
    input in4
);
*/
module top_module (
    input a,
    input b,
    input c,
    input d,
    output out1,
    output out2
);
    mod_a inst1 (
        .in1(a),
        .in2(b),
        .in3(c),
        .in4(d),
        .out1(out1),
        .out2(out2)
    );
    // out1, out2처럼 이름과 방향이 겹치는 출력 포트를 연결할 때
    // 포지션 기반 표현은 컴파일 오류 발생
endmodule
```

📌 **포인트**  
- **포지션 기반 연결은 포트 방향이 겹치면 오류가 날 수 있음**  
- 이름 기반 연결이 더 안정적이며, 가독성과 유지보수성이 뛰어남

---

## ✅ Three modules

```verilog
/*
module my_dff (
    input clk,
    input d,
    output q
);
*/
module top_module (
    input clk,
    input d,
    output q
);
    wire q1;
    wire q2;
    my_dff d1 (.clk(clk), .d(d), .q(q1));
    my_dff d2 (.clk(clk), .d(q1), .q(q2));
    my_dff d3 (.clk(clk), .d(q2), .q(q));

endmodule
```

📌 **포인트**  
- D 플립플롭 3개를 **직렬로 연결하여 3-stage shift register** 구성  
- 내부 연결을 위해 **wire 신호를 선언하고 연결**

---

## ✅ Modules and vectors

```verilog
/*
module my_dff8(
    input clk,
    input [7:0] d,
    output [7:0] q
);
*/
module top_module (
    input clk,
    input [7:0] d,
    input [1:0] sel,
    output [7:0] q
);
    wire [7:0] q1;
    wire [7:0] q2;
    wire [7:0] q3;

    my_dff8 d1(.clk(clk), .d(d), .q(q1));
    my_dff8 d2(.clk(clk), .d(q1), .q(q2));
    my_dff8 d3(.clk(clk), .d(q2), .q(q3));

    always @(*) begin
        case(sel)
            2'b00: q = d;
            2'b01: q = q1;
            2'b10: q = q2;
            2'b11: q = q3;
        endcase
    end

endmodule
```

📌 **포인트**  
- 8비트 벡터를 처리하는 D 플립플롭을 **3단 연결 후 MUX로 출력 선택**  
- `always @(*)`와 `case`를 이용한 **조합 논리 구현**

---

## ✅ Adder 1

```verilog
/*
module add16 (
    input [15:0] a,
    input [15:0] b,
    input cin,
    output [15:0] sum,
    output cout
);
*/
module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
);
    wire cout_lo;
    wire [15:0] sum_hi;
    wire [15:0] sum_lo;
    
    add16 add_lo (
        .a(a[15:0]),
        .b(b[15:0]),
        .cin(1'b0),
        .sum(sum_lo),
        .cout(cout_lo)
    );

    add16 add_hi (
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(cout_lo),
        .sum(sum_hi),
        .cout()
    );

    assign sum = {sum_hi, sum_lo};

endmodule
```

📌 **포인트**  
- 16비트 가산기를 **두 번 사용하여 32비트 가산기 구성**  
- carry-out을 다음 단계의 carry-in으로 연결 (ripple carry 구조)

---

## ✅ Adder 2

```verilog
/*
module add16 (
    input [15:0] a,
    input [15:0] b,
    input cin,
    output [15:0] sum,
    output cout
);
*/
module add1 (
    input a,
    input b,
    input cin,
    output sum,
    output cout
);
    assign sum = a ^ b ^ cin;
    assign cout = (a & b) | (a & cin) | (b & cin);
endmodule

module top_module (
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
);
    wire cout_lo;
    wire [15:0] sum_lo;
    wire [15:0] sum_hi;

    add16 add_lo (
        .a(a[15:0]),
        .b(b[15:0]),
        .cin(1'b0),
        .sum(sum_lo),
        .cout(cout_lo)
    );

    add16 add_hi (
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(cout_lo),
        .sum(sum_hi),
        .cout()
    );

    assign sum = {sum_hi, sum_lo};

endmodule    
```

📌 **포인트**  
- `add1`은 1비트 전가산기, 이를 기반으로 `add16` 구성  
- `top_module`은 `add16`을 두 번 호출해 32비트 덧셈 구현

---

## ✅ Carry-select adder

```verilog
/*
module add16 (
    input [15:0] a,
    input [15:0] b,
    input cin,
    output [15:0] sum,
    output cout
);
*/
module top_module (
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
);
    wire cout_lo;
    wire [15:0] sum_lo;
    wire [15:0] sum_hi;
    wire [15:0] sum_hi1;
    wire [15:0] sum_hi2;

    add16 add_lo (
        .a(a[15:0]),
        .b(b[15:0]),
        .cin(1'b0),
        .sum(sum_lo),
        .cout(cout_lo)
    );
    add16 add_hi1 (
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b0),
        .sum(sum_hi1),
        .cout()
    );
    add16 add_hi2 (
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b1),
        .sum(sum_hi2),
        .cout()
    );

    assign sum_hi = (cout_lo) ? sum_hi2 : sum_hi1;
    assign sum = {sum_hi, sum_lo};

endmodule
```

📌 **포인트**  
- carry-select 구조: 두 경우(carry-in=0, 1)를 미리 계산 후 선택  
- delay를 줄이는 빠른 덧셈 회로 기법 구현

---

## ✅ Adder-subtractor

```verilog
/*
module add16 (
    input [15:0] a,
    input [15:0] b,
    input cin,
    output [15:0] sum,
    output cout
);
*/
module top_module (
    input [31:0] a,
    input [31:0] b,
    input sub,
    output [31:0] sum
);
    wire cout_lo;
    wire [31:0] b_xored;
    wire [15:0] sum_lo;
    wire [15:0] sum_hi;

    assign b_xored = b ^ {32{sub}};

    add16 add_lo (
        .a(a[15:0]),
        .b(b_xored[15:0]),
        .cin(sub),
        .sum(sum_lo),
        .cout(cout_lo)
    );
    add16 add_hi (
        .a(a[31:16]),
        .b(b_xored[31:16]),
        .cin(cout_lo),
        .sum(sum_hi),
        .cout()
    );

    assign sum = {sum_hi, sum_lo};

endmodule
```

📌 **포인트**  
- `sub`가 1이면 `b`를 반전하고 carry-in을 1로 더해 **뺄셈 구현**  
- `b ^ {32{sub}}`는 **sign-extension 방식의 XOR 연산**
