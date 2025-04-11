---
layout: post
title: "[Verilog] Procedures"
category: education
---

---

# 📘 Procedures

---

## ✅ Always blocks (combinational)

```verilog
module top_module(
    input a, 
    input b,
    output wire out_assign,
    output reg out_alwaysblock
);

    assign out_assign = a & b;

    always @(*) begin
        out_alwaysblock = a & b;
    end

endmodule
```

📌 **포인트**  
- `assign`은 wire에만 사용, `always`는 reg 변수에 사용  
- combinational always block은 `always @(*)`로 선언  
- assign과 always block은 동일한 조합 논리를 생성할 수 있음  

---

## ✅ Always blocks (clocked)

```verilog
module top_module(
    input clk,
    input a,
    input b,
    output wire out_assign,
    output reg out_always_comb,
    output reg out_always_ff
);

    assign out_assign = a ^ b;

    always @(*) begin
        out_always_comb = a ^ b;
    end

    always @(posedge clk) begin
        out_always_ff <= a ^ b;
    end

endmodule
```

📌 **포인트**  
- `always @(*)`는 조합논리, `always @(posedge clk)`는 순차논리  
- clocked always block에서는 non-blocking assignment (`<=`) 사용해야 안전함  
- 시뮬레이터와 합성 결과를 일치시키기 위해 반드시 규칙 준수할 것

---

## ✅ If statement

```verilog
module top_module(
    input a,
    input b,
    input sel_b1,
    input sel_b2,
    output wire out_assign,
    output reg out_always
);

    assign out_assign = (sel_b1 & sel_b2) ? b : a;

    always @(*) begin
        if (sel_b1 & sel_b2)
            out_always = b;
        else
            out_always = a;
    end

endmodule
```

📌 **포인트**  
- `assign`의 삼항 연산자와 `always`의 if문은 동일한 논리를 표현할 수 있음  
- if문에서 출력에 항상 값이 할당되어야 래치를 피할 수 있음

---

## ✅ If statement latches

```verilog
module top_module (
    input      cpu_overheated,
    output reg shut_off_computer,
    input      arrived,
    input      gas_tank_empty,
    output reg keep_driving
);

    always @(*) begin
        if (cpu_overheated)
            shut_off_computer = 1;
        else
            shut_off_computer = 0;
    end

    always @(*) begin
        if (~arrived)
            keep_driving = ~gas_tank_empty;
        else
            keep_driving = 0;
    end

endmodule
```

📌 **포인트**  
- if문에서 **모든 조건에 대해 출력이 할당되지 않으면 래치가 생김**  
- else 절을 반드시 포함하거나, 초기화 값을 항상 주는 방식으로 방지  
- 래치는 의도적이 아니라면 대부분 버그로 간주됨

---

## ✅ Case statement

```verilog
module top_module ( 
    input [2:0] sel, 
    input [3:0] data0,
    input [3:0] data1,
    input [3:0] data2,
    input [3:0] data3,
    input [3:0] data4,
    input [3:0] data5,
    output reg [3:0] out );

    always @(*) begin
        case (sel)
            3'd0: out = data0;
            3'd1: out = data1;
            3'd2: out = data2;
            3'd3: out = data3;
            3'd4: out = data4;
            3'd5: out = data5;
            default: out = 4'd0;
        endcase
    end

endmodule
```

📌 **포인트**  
- case 문은 if-else보다 가독성이 좋고, 많은 선택지를 다룰 때 편리  
- `endcase`로 꼭 닫아야 하며, default는 선택적으로 사용  
- 항상 모든 조건에서 출력값이 정의되어야 래치를 피할 수 있음

---

## ✅ Priority encoder

```verilog
module top_module (
    input [3:0] in,
    output reg [1:0] pos
);

    always @(*) begin
        if (in[0])
            pos = 2'd0;
        else if (in[1])
            pos = 2'd1;
        else if (in[2])
            pos = 2'd2;
        else if (in[3])
            pos = 2'd3;
        else
            pos = 2'd0;
    end

endmodule
```

📌 **포인트**  
- 여러 입력 중 우선순위가 가장 높은 1의 인덱스를 출력  
- if문은 우선순위를 표현하기 쉬운 구조지만, 모든 경우를 처리해야 함  
- 마지막 else가 없으면 래치 발생 위험 있음

---

## ✅ Priority encoder with casez

```verilog
module top_module (
    input [7:0] in,
    output reg [2:0] pos
);

    always @(*) begin
        casez (in)
            8'bzzzzzzz1: pos = 3'd0;
            8'bzzzzzz10: pos = 3'd1;
            8'bzzzzz100: pos = 3'd2;
            8'bzzzz1000: pos = 3'd3;
            8'bzzz10000: pos = 3'd4;
            8'bzz100000: pos = 3'd5;
            8'bz1000000: pos = 3'd6;
            8'b10000000: pos = 3'd7;
            default:     pos = 3'd0;
        endcase
    end
endmodule
```

📌 **포인트**  
- `casez`는 `z` 또는 `?`를 **don't care 비트로 취급**하여 비교  
- 가장 우선순위 높은 1의 위치를 찾기 위한 간결한 방법  
- 첫 번째 매칭 항목이 선택됨 → 순서가 중요함!  

---

## ✅ Avoiding latches

```verilog
module top_module (
    input [15:0] scancode,
    output reg left,
    output reg down,
    output reg right,
    output reg up
);

    always @(*) begin
        up = 0; down = 0; left = 0; right = 0;
        case (scancode)
            16'he06b: left = 1;
            16'he072: down = 1;
            16'he074: right = 1;
            16'he075: up = 1;
        endcase
    end

endmodule
```

📌 **포인트**  
- 모든 출력에 대해 **기본값을 먼저 설정** → 이후 case에서 필요한 것만 수정  
- 이렇게 하면 default가 없어도 latch가 생기지 않음  
- scancode를 기반으로 방향키 입력 인식하는 회로  
