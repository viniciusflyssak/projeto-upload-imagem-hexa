unit uFrmPrincipal;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.ExtCtrls, Vcl.StdCtrls, Vcl.Buttons,
  FireDAC.Stan.Intf, FireDAC.Stan.Option, FireDAC.Stan.Param,
  FireDAC.Stan.Error, FireDAC.DatS, FireDAC.Phys.Intf, FireDAC.DApt.Intf,
  FireDAC.Stan.Async, FireDAC.DApt, Data.DB, FireDAC.Comp.DataSet,
  FireDAC.Comp.Client, uDM, Vcl.Imaging.pngimage;

type
  TfrmPrincipal = class(TForm)
    pnlFundo: TPanel;
    imgEnviar: TImage;
    pnlBotoes: TPanel;
    btnEnviar: TSpeedButton;
    edtTitulo: TEdit;
    procedure btnEnviarClick(Sender: TObject);
    function geraHexImagem: String;
    procedure imgEnviarClick(Sender: TObject);
    procedure FormCreate(Sender: TObject);
    procedure edtTituloKeyPress(Sender: TObject; var Key: Char);
    procedure FormResize(Sender: TObject);
    
  private
    FArquivoSelecionado: String;
  public
    { Public declarations }
  end;

var
  frmPrincipal: TfrmPrincipal;

implementation

{$R *.dfm}

procedure TfrmPrincipal.btnEnviarClick(Sender: TObject);
var
  qryInserir: TFDQuery;
begin
  if trim(edtTitulo.Text) = '' then
    raise Exception.Create('Adicione um título!');
  if FArquivoSelecionado = '' then
    raise Exception.Create('Selecione ao menos uma imagem!');
  qryInserir := TFDQuery.Create(nil);
  try
    qryInserir.Connection := DM.Connection;
    qryInserir.SQL.Add(' insert into imagens values (default, :pTitulo, :pImagem) ');
    qryInserir.ParamByName('pTitulo').Value := edtTitulo.Text;
    qryInserir.ParamByName('pImagem').AsBlob := geraHexImagem;
    qryInserir.ExecSQL;
  finally
    FreeAndNil(qryInserir);
  end;
  ShowMessage('Imagem inserida com sucesso!');
end;

procedure TfrmPrincipal.edtTituloKeyPress(Sender: TObject; var Key: Char);
begin
  if Key = #$D then
    btnEnviarClick(Sender);
end;

procedure TfrmPrincipal.FormCreate(Sender: TObject);
begin
  FArquivoSelecionado := '';
end;

procedure TfrmPrincipal.FormResize(Sender: TObject);
begin
  edtTitulo.Width := btnEnviar.Left - 10;
end;

function TfrmPrincipal.geraHexImagem: String;
  function StreamToHexStr(AStream: TStream): String;
  const
   HexArr: array[0..15] of char =
   ('0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F');
  var
   AByte: Byte;
   i: Integer;
  begin
   SetLength(Result, AStream.Size * 2);
   AStream.Position := 0;
   for i := 0 to AStream.Size - 1 do
   begin
     AStream.ReadBuffer(AByte, SizeOf(AByte));
     Result[i * 2 + 1] := HexArr[AByte shr 4];
     Result[i * 2 + 2] := HexArr[AByte and $0F];
   end;
  end;
var
   Stream: TMemoryStream;
begin
  Stream := TMemoryStream.Create;
  try
    //  imgEnviar.Picture.ToString(Stream);
    imgEnviar.Picture.Graphic.SaveToStream(Stream);
    Result := StreamToHexStr(Stream);
  finally
    Stream.Free;
  end;
end;

procedure TfrmPrincipal.imgEnviarClick(Sender: TObject);
  var 
    png: TPngImage;
  function selecionaArquivo: String;  
  var
    selectedFile: string;
    dlg: TOpenDialog;
  begin
    selectedFile := '';
    dlg := TOpenDialog.Create(nil);
    try
      dlg.InitialDir := 'C:\';
      dlg.Filter := 'All files (*.*)|*.*';
      if dlg.Execute(Handle) then
        selectedFile := dlg.FileName;
    finally
      dlg.Free;
    end;

    if selectedFile <> '' then
      Result := selectedFile;
  end;
begin
  FArquivoSelecionado := selecionaArquivo;
  if FArquivoSelecionado = '' then
    exit;  
  png := TPngImage.Create;
  try
    png.LoadFromFile(FArquivoSelecionado);
    imgEnviar.Picture.Graphic := png;
    imgEnviar.Proportional := True;
    imgEnviar.Center := True;
  finally
    png.Destroy;
  end;
end;

end.
