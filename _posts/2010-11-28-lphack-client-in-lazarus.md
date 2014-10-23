---
author: tris
comments: true
date: 2010-11-28 16:27:56+00:00
layout: post
slug: lphack-client-in-lazarus
title: lpHack Client in Lazarus
wordpress_id: 29
categories:
- Programming
---

At the first hack night, I wrote a small GUI frontend to the [LPD music Hack](http://canthack.org/2010/11/lpd-music-hack-how-its-done/) print server that Edd had set up on a laptop, to make it easier for people to submit tracks to the jukebox style music queue.

Edd provided all of us at the hack night with a shell script to both queue music via ssh and get the current playlist.

I then used Lazarus (see below) to create a small GUI to call the script after selecting a file, and a TTimer object to periodically poll the playlist and display it in a TMemo.  When I have access to the server again I will update the screenshot to show the playlist. I also added a little TProgressBar to give feedback to the user while the script is running.

[caption id="attachment_37" align="alignnone" width="403" caption="The lpClient GUI"][![lpClient screenshot](http://canthack.org/uploads/lpclient.png)](http://canthack.org/2010/11/lphack-client-in-lazarus/lpclient/)[/caption]

**Lazarus**

Lazarus is a cross-platform IDE for the Freepascal compiler, and is similar to Embarcadero Delphi. It is a RAD application development environment and   allows developers to write code once and then compile it on multiple operating systems and toolkits, for example   Windows(Win32/64 or QT), Mac OS X (Carbon), or Linux (GTK, GTK2 or QT),  which is really useful for doing cross platform applications which run  natively (with no need for a virtual machine).  More information can be found at [http://www.lazarus.freepascal.org/](http://www.lazarus.freepascal.org/)

The lpHack code can be found after the break.

<!-- more -->
[sourcecode language="delphi"]
unit main;

{$mode objfpc}{$H+}

interface

uses
  Classes, SysUtils, FileUtil, LResources, Forms, Controls,
  Graphics, Dialogs, StdCtrls, ComCtrls, ExtCtrls, Process;

type

  { TForm1 }

  TForm1 = class(TForm)
    Button1: TButton;
    Label4: TLabel;
    Memo1: TMemo;
    OpenDialog1: TOpenDialog;
    ProgressBar1: TProgressBar;
    Timer1: TTimer;
    procedure Button1Click(Sender: TObject);
    procedure Timer1Timer(Sender: TObject);
  private
    { private declarations }
  public
    { public declarations }
  end; 

var
  Form1: TForm1; 

implementation

{ TForm1 }

procedure TForm1.Button1Click(Sender: TObject);
var
  p: TProcess;
begin
  if OpenDialog1.Execute then
  begin
    p := TProcess.Create(Self);
    ProgressBar1.Visible := True;

    try
      p.CommandLine := ProgramDirectory + 'q2.sh ' + '"' +
        OpenDialog1.FileName + '"';

      p.Execute;

      while p.Active do
      begin
        ProgressBar1.StepBy(10);
        Application.ProcessMessages;
        Sleep(100);

        if ProgressBar1.Position = Progressbar1.Max then
          ProgressBar1.Position := ProgressBar1.Min;

      end;

      ProgressBar1.Position := Progressbar1.Max;
      Label4.Visible := True;
      Application.ProcessMessages;
      Sleep(200);

    finally
      p.Free;
      ProgressBar1.Visible := False;
      Label4.Visible := False;
      Application.ProcessMessages;
    end;

  end;
end;

procedure TForm1.Timer1Timer(Sender: TObject);
var
  p: TProcess;
  i: Integer;
begin
    Timer1.Interval := 2000;
    p := TProcess.Create(Self);

    try
      p.Options := [poWaitonExit];

      p.CommandLine := ProgramDirectory +
        'q2.sh -l /tmp/lpPlaylist.log';

      p.Execute;
      Memo1.Clear;

      if FileExists('/tmp/lpPlaylist.log') then
      begin
        Memo1.Lines.LoadFromFile('/tmp/lpPlaylist.log');

        for i := 0 to Memo1.Lines.Count - 1 do
        begin
          Memo1.Lines.Strings[i] := Format(' %.2d  %s',
            [i+1, Memo1.Lines.Strings[i]]);

        end;
      end
      else
        Memo1.Lines.Add('Playlist not found');

    finally
      p.Free;
      Application.ProcessMessages;
    end;
end;

initialization
  {$I main.lrs}
end.
[/sourcecode]
