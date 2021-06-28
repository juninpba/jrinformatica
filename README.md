caixa_remoto.Close;
  caixa_remoto.ParamByName('data').AsDate := Date;
  caixa_remoto.Open;   {Filtra a consulta caixa  local }

   atualizar_remoto_caixa.Close;
   atualizar_remoto_caixa.ParamByName('saldocaixa').AsFloat :=  caixa_remotoSALDO.AsFloat;
   atualizar_remoto_caixa.ParamByName('dataatual').AsString := DateToStr(date);
   atualizar_remoto_caixa.ParamByName('GET').AsString := EMPRESA_SISTEMACODE_GET.AsString;
   atualizar_remoto_caixa.Prepare;
   atualizar_remoto_caixa.ExecSQL;    {atualiza apenas o saldo do caixa, não é forma de pagamento }
   


   atualizar_formas_pag_remota.Close;
   atualizar_formas_pag_remota.ParamByName('data').AsDate := Date;
   atualizar_formas_pag_remota.Open; {Filtra a consulta das formas de pagamento local }


   deletar_remoto_form.Close;
   deletar_remoto_form.SQL.Clear;
   deletar_remoto_form.SQL.Add('delete from FORMAS_PAG_EMPRESAS where DATA_MOVIMENTO =:dataatual and COD_GET =:COD ');
   deletar_remoto_form.ParamByName('COD').AsString := EMPRESA_SISTEMACODE_GET.AsString;
   deletar_remoto_form.ParamByName('dataatual').AsString :=  FormatDateTime('yyyy-mm-dd',DATE);
   deletar_remoto_form.Prepare;
   deletar_remoto_form.ExecSQL;   {deleta as formas de pagamento pelo cod_empresa com a data atual }

    deletar_remoto_form.Close;
   deletar_remoto_form.SQL.Clear;
   deletar_remoto_form.SQL.Add('ALTER TABLE FORMAS_PAG_EMPRESAS AUTO_INCREMENT=0');
   deletar_remoto_form.Prepare;
   deletar_remoto_form.ExecSQL;  {ele irá resetar a contagem do ID (incrementado) }


   atualizar_formas_pag_remota.First;
   while NOT  atualizar_formas_pag_remota.EOF  do   { Looping para inserir as formas de pagamento da empresa }
   BEGIN
     pagamento_remoto.Close;
     pagamento_remoto.ParamByName('COD').AsString := EMPRESA_SISTEMACODE_GET.AsString;
     pagamento_remoto.ParamByName('dataatual').AsString := DateToStr(Date);
     pagamento_remoto.OpeN;  {Ele pega a consulta de forma de pagamento remoto e insere em baixo }

    pagamento_remoto.Insert;
    pagamento_remotoDATA_MOVIMENTO.AsString := DateToStr(Date);
    pagamento_remotoDESCRICAO.AsString := atualizar_formas_pag_remotaDESCRICAO.AsString;
    pagamento_remotoVALOR.AsFloat := atualizar_formas_pag_remotaSOMA_TOTAL.AsFloat;
    pagamento_remotoCOD_GET.AsString := EMPRESA_SISTEMACODE_GET.AsString;
    pagamento_remotoQUANTIDADE.AsInteger := atualizar_formas_pag_remotaCONTADOR.AsInteger;
    pagamento_remoto.Post;
     atualizar_formas_pag_remota.Next;
   END;
