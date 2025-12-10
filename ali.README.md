ALU 테스트
```
module ALU(
input wire [31:0] SrcA, SrcB,
input wire [2:0] ALUcontrol,
output wire zero,
output reg [31:0] ALUresult
);
always @ (*) begin
case (ALUcontrol)
3'b000: ALUresult = SrcA + SrcB;
default ALUresult = 0;
endcase
end

endmodule
module SingleCycleProcessor();
wire [31:0] PC, PCNext, Instr, ImmExt, SrcA, SrcB, ALUresult;
reg CLK = 0, RST = 0;
reg [2:0] ALUcontrol = 0;
ProgramCounter pc(.CLK(CLK), .RST(RST), .PCNext(PCNext), .PC(PC));
InstructionMemory im(.A(PC), .RD(Instr));
RegisterFile rf(.A1(Instr[19:15]), .RD1(SrcA), .A2(), .RD2(), .A3(), .WD3(), .WE3());
assign ImmExt = {{20{Instr[31]}}, Instr[31:20]};
ALU alu(.SrcA(SrcA), .SrcB(SrcB), .ALUresult(ALUresult), .ALUcontrol(ALUcontrol));
assign SrcB = ImmExt;

initial begin
#100 RST = 1;
#100 RST = 0;
end

endmodule
```

```
module ALU(
input wire [31:0] SrcA, SrcB,
input wire [2:0] ALUcontrol,
output wire zero,
output reg [31:0] ALUresult
);
always @ (*) begin
case (ALUcontrol)
3'b000: ALUresult = SrcA + SrcB;
default ALUresult = 0;
endcase
end

endmodule
module SingleCycleProcessor();
wire [31:0] PC, PCNext, Instr, ImmExt, SrcA, SrcB, ALUresult, ReadData, PCplus4;
reg CLK = 0, RST = 0;
reg [2:0] ALUcontrol = 0;
reg Regwrite =0;
ProgramCounter pc(.CLK(CLK), .RST(RST), .PCNext(PCNext), .PC(PC));
InstructionMemory im(.A(PC), .RD(Instr));
RegisterFile rf(.CLK(CLK),.A1(Instr[19:15]), .RD1(SrcA), .A2(), .RD2(), .A3(Instr[11:7]), .WD3(ReadData), .WE3());

assign ImmExt = {{20{Instr[31]}}, Instr[31:20]};

ALU alu(.SrcA(SrcA), .SrcB(SrcB), .ALUresult(ALUresult), .ALUcontrol(ALUcontrol));
assign SrcB = ImmExt;

DataMemory dm(.CLK(CLK), .A(ALUresult), .RD(ReadData),.WE(),.WD());

assign PCplus4 = PC+4;
assign PCNext=PCplus4;

initial begin
#100 RST = 1;
#100 RST = 0;
#100 Regwrite = 1;
#10 CLK = 1;
end
···
