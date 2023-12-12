unit Unit2;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, StdCtrls, Grids;

type
  TForm2 = class(TForm)
    StringGrid1: TStringGrid;
    StringGrid2: TStringGrid;
    StringGrid3: TStringGrid;
    Edit1: TEdit;
    Edit2: TEdit;
    Button1: TButton;
    Label4: TLabel;
    Label5: TLabel;
    Label1: TLabel;
    Label3: TLabel;
    Label2: TLabel;
    procedure Button1Click(Sender: TObject);
    procedure FormCreate(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

type Vector = array[1..100] of Real;
     Matrix = array[1..100] of Vector;

var
  Form2: TForm2;

implementation

{$R *.dfm}

procedure LoadFile(var n: Byte; var a: Matrix);
var
  f: TextFile;
  i, j: Byte;
begin
  AssignFile(f, 'matrix.txt');
  Reset(f);
  ReadLn(f, n);
  for i := 1 to n do
    for j := 1 to n do
      Read(f, a[i, j]);
  CloseFile(f);
end;

procedure Proisv(n: Byte; a, b: Matrix; var result: Matrix);
var
  i, j, k: Byte;
begin
  for i := 1 to n do
    for j := 1 to n do
    begin
      result[i, j] := 0;
      for k := 1 to n do
        result[i, j] := result[i, j] + a[i, k] * b[k, j];
    end;
end;

procedure Trans(x: Matrix; n: Byte; var xtr: Matrix);
var
  i, j: Byte;
begin
  for i := 1 to n do
    for j := 1 to n do
      xtr[i, j] := x[j, i];
end;

procedure methodvrashenii(n: byte; eps: real; a: matrix; var k: integer;
  var lambda: vector; var v: matrix);
var
  i, j, p, q: byte;
  alpha, beta, gamma: real;
  tau, t, c, s: real;
  a1, v1: matrix;
  flag: boolean;
begin
  p := 1;
  q := 2;
  k := 1;
  repeat
    alpha := 0;
    beta := 0;
    gamma := 0;
    for i := 1 to n do
      for j := i + 1 to n do
      begin
        if abs(a[i, j]) > eps then
        begin
          flag := false;
          alpha := a[i, i];
          beta := a[i, j];
          gamma := a[j, j];
          flag := true;
        end;
        if flag then
        begin
          tau := (gamma - alpha) / (2 * beta);
          if tau > 0 then
            t := 1 / (tau + sqrt(1 + sqr(tau)))
          else
            t := -1 / (-tau + sqrt(1 + sqr(tau)));
          c := 1 / sqrt(sqr(t) + 1);
          s := t * c;
        // èñïðàâëåíèå îøèáêè
          begin
            v1[i, p] := c * a[i, p] - s * a[i, q];
            v1[i, q] := s * a[i, p] + c * a[i, q];
          end;

          begin
            a1[p, i] := c * v1[p, i] - s * v1[q, i];
            a1[q, i] := s * v1[p, i] + c * v1[q, i];
          end;

          begin
            a[i, p] := a1[i, p];
            a[i, q] := a1[i, q];
          end;

            a[p, i] := a1[p, i];
          flag := false;
        end;
      end;
    lambda[k] := a[p, p];
    for i := 2 to n do
      if lambda[k] < a[i, i] then
        lambda[k] := a[i, i];
    k := k + 1;
  until abs(a[p, q]) < eps;
  for i := 1 to n do
    for j := 1 to n do
      v[i, j] := v1[j, i];
end;

procedure Vyvod(n: Byte; filename: string; a, v: Matrix; lambda: Vector);
var
  f: TextFile;
  i, j: Integer;
begin
  AssignFile(f, filename);
  Rewrite(f);
  Writeln(f, 'Ñîáñòâåííûå çíà÷åíèÿ ìàòðèöû:');
  for i := 1 to n do
    Writeln(f, '?', i, ' = ', lambda[i]);
  Writeln(f, '');
  Writeln(f, 'Ñîáñòâåííûå âåêòîðû ìàòðèöû:');
  for i := 1 to n do
  begin
    Write(f, 'v', i, ' = ');
    for j := 1 to n do
      Write(f, v[i, j]:10:6, ' ');
    Writeln(f);
  end;
  CloseFile(f);
end;


procedure TForm2.Button1Click(Sender: TObject);
var
  n, k: Byte;
  eps: Real;
  a, v, v1: Matrix;
  lambda: Vector;
begin
    eps := StrToFloat(Edit1.Text);
  LoadFile(n, a);
  MethodVrashenii(n, eps, a, k, lambda, v);
  Vyvod(n, 'output.txt', a, v, lambda);
  StringGrid1.ColCount := n;
  StringGrid1.RowCount := n;
  StringGrid2.ColCount := n;
  StringGrid2.RowCount := n;
  StringGrid3.ColCount := 1;
  StringGrid3.RowCount := n;
  for i := 0 to n-1 do
    for j := 0 to n-1 do
      StringGrid1.Cells[j, i] := FloatToStr(a[i+1, j+1]);
  for i := 0 to n-1 do
    for j := 0 to n-1 do
    begin
      v1[i+1, j+1] := v[j+1, i+1];
      StringGrid2.Cells[j, i] := FloatToStr(v1[i+1, j+1]);
    end;
  for i := 0 to n-1 do
    StringGrid3.Cells[0, i] := FloatToStr(lambda[i+1]);
end;

procedure TForm2.FormCreate(Sender: TObject);
var
  i, j, n: Byte;
  a: Matrix;
begin
  LoadFile(n, a);
  StringGrid1.ColCount := n;
  StringGrid1.RowCount := n;
  StringGrid2.ColCount := n;
  StringGrid2.RowCount := n;
  StringGrid3.ColCount := 1;
  StringGrid3.RowCount := n;
  for i := 1 to n do
    for j := 1 to n do
      StringGrid1.Cells[i-1, j-1] := FloatToStr(a[i, j]);
end;

end.

