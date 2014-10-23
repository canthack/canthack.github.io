---
author: sav
comments: true
date: 2010-12-26 12:29:14+00:00
layout: post
slug: basic-template-for-multithread-client-server-gui
title: Basic template for multithread client-server GUI
wordpress_id: 249
categories:
- Programming
---

Below is included Java source code for a simple GUI that launches a local-hosted server and any number of clients run on separate threads and displays the client-side and server-side output in two text areas, including text sent client to server and vice versa.

It is a simple proof-of-concept application that so far does nothing useful: each thread merely waits a few seconds. But it could be expanded upon to add arbitrary functionality.

Code after the break...

<!-- more -->
[sourcecode language="java"]
//Implement GUI showing outputs of client and server in a GUI in seperate JTextAreas

import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;
import java.awt.Font;
import javax.swing.Box;
import javax.swing.JFrame;
import javax.swing.JTextArea;
import javax.swing.JTextField;
import javax.swing.JButton;
import javax.swing.JScrollPane;

import java.net.*;
import java.io.*;
import java.util.*;

class GUIFrame extends JFrame
{
protected JTextArea textArea1;
protected JTextArea textArea2;
protected JTextField textField1;
protected JTextField textField2;
protected String clientText;
protected String serverText;

public GUIFrame()
{
super( "Client and Server Outputs" );
Box box = Box.createHorizontalBox();
Box innerBox1 = Box.createVerticalBox();
Box innerBox2 = Box.createVerticalBox();
Box innerBoxMid = Box.createVerticalBox();
ClSeGui7.staticClientText = " Output of client to itself indicated CLIENT:\n" +
	" Response from server sent to client indicated SERVER:\n\n"; 

ClSeGui7.staticServerText = " Output of server to itself indicated SERVER:\n" +
	" Message from client sent to server indicated CLIENT:\n\n";

Font boldFont = new Font( "Serif", Font.BOLD, 18);
//Set up client-side tex area
textArea1 = new JTextArea( ClSeGui7.staticClientText, 50, 75);
textField1 = new JTextField( "   CLIENT", 30 );
textField2 = new JTextField( "   SERVER", 30 );
textField1.setFont( boldFont );
textField2.setFont( boldFont );
innerBox1.add( textField1 );
innerBox1.add( new JScrollPane( textArea1 ) );
box.add( innerBox1 );

JButton button1 = new JButton( "Initiate Server" );
JButton button2 = new JButton( "New Client Connect" );
innerBoxMid.add( button1 );
innerBoxMid.add( button2 );
box.add( innerBoxMid );

button1.addActionListener(
	new ActionListener()
	{
	public void actionPerformed( ActionEvent event)
		{
		ClSeGui7.initiateServer = true;
		}
	}
);

button2.addActionListener(
	new ActionListener()
	{
		public void actionPerformed( ActionEvent event)
		{
		ClSeGui7.connectClient = true;
		ClSeGui7.anotherClient = true;
		}
	}
);

textArea2 = new JTextArea( ClSeGui7.staticServerText, 50, 75);
innerBox2.add( textField2 );
innerBox2.add( new JScrollPane( textArea2 ) );
box.add( innerBox2 );

add( box );
}
}

class Client {

private GUIFrame frame;

Client( GUIFrame frame ) {
  	this.frame = frame;
  }

public static void runClient(GUIFrame frame) {

String host = "localhost";
int port = 19999;
StringBuffer instr = new StringBuffer();
String TimeStamp;
System.out.println(" CLIENT:  Client " + ClSeGui7.clientCount + " initialized");
ClSeGui7.staticClientText = ClSeGui7.staticClientText + " CLIENT:  Client "
                                                 + ClSeGui7.clientCount + " Initialized\n\n";
//Write o/p to GUI client-side
frame.textArea1.setText( ClSeGui7.staticClientText );
try {
      InetAddress address = InetAddress.getByName(host);
      Socket connection = new Socket(address, port);

      BufferedOutputStream bos = new BufferedOutputStream(connection.	getOutputStream());
      OutputStreamWriter osw = new OutputStreamWriter(bos, "US-ASCII");
      TimeStamp = new java.util.Date().toString();
      String process = " CLIENT no. " + ClSeGui7.clientCount + ":  Calling the Server on "
                                   + host + " port " + port + " at " + TimeStamp +  (char) 13;

      osw.write(process);
      //Write o/p to GUI server-side
     ClSeGui7.staticServerText = ClSeGui7.staticServerText + process +"\n\n";
     frame.textArea2.setText( ClSeGui7.staticServerText );
     osw.flush();

     BufferedInputStream bis = new BufferedInputStream(connection.getInputStream());
     InputStreamReader isr = new InputStreamReader(bis, "US-ASCII");

     int c;
     while ( (c = isr.read()) != 13)
     instr.append( (char) c);

     connection.close();
     System.out.println(instr);
     	}
     catch (IOException f) {
     System.out.println("IOException: " + f);
    	}
     catch (Exception g) {
     System.out.println("Exception: " + g);
    		}
  	}
}

class Server implements Runnable {

private Socket connection;
private String TimeStamp;
private int ID;
private GUIFrame frame;

Server(Socket s, int i, GUIFrame frame) {
this.connection = s;
this.ID = i;
this.frame = frame;
}

public static void runServer(GUIFrame frame) {
int port = 19999;
int count = 0;
try{
   ServerSocket socket1 = new ServerSocket(port);
   System.out.println(" SERVER:  Server Initialized");
   ClSeGui7.staticServerText = ClSeGui7.staticServerText + " SERVER:  Server Initialized\n\n";
   //Write o/p to GUI server-side
   frame.textArea2.setText( ClSeGui7.staticServerText );
   while (true) {
        Socket connection = socket1.accept();
        Runnable runnable = new Server(connection, ++count, frame);
        Thread thread = new Thread(runnable);
        thread.start();
      }
    }
    catch (Exception e) {}
  }

public void run() {
try {
   BufferedInputStream is = new BufferedInputStream(connection.getInputStream());
   InputStreamReader isr = new InputStreamReader(is);
   int character;
   StringBuffer process = new StringBuffer();
   while((character = isr.read()) != 13) {
        process.append((char)character);
	      	}
   System.out.println(process);
   try {
       Thread.sleep(5000);
	      	}
    catch (Exception e){}
    TimeStamp = new java.util.Date().toString();
    String returnCode = " SERVER:  Server responded at "+ TimeStamp + (char) 13;
    BufferedOutputStream os = new BufferedOutputStream(connection.getOutputStream());
    OutputStreamWriter osw = new OutputStreamWriter(os, "US-ASCII");
    osw.write(returnCode);
    //Write o/p to GUI client-side
    ClSeGui7.staticClientText = ClSeGui7.staticClientText + returnCode + "\n\n";
    frame.textArea1.setText( ClSeGui7.staticClientText );
    osw.flush();
    	}
catch (Exception e) {
   System.out.println(e);
    	}
finally {
   try {
     connection.close();

        }catch (IOException e){}
    }
  }
}

class ServerThread implements Runnable {

	private GUIFrame frame;

	ServerThread(GUIFrame frame) {
	this.frame = frame;
	}	

	public void run() {
	Server.runServer(frame);
	}
}

class ClientThread implements Runnable {

	private GUIFrame frame;

	ClientThread(GUIFrame frame) {
	this.frame = frame;
	}
	public void run() {
	Client.runClient(frame);
	}
}

public class ClSeGui7
{

	protected static String staticClientText;
	protected static String staticServerText;
	protected static boolean connectClient = false;
	protected static boolean initiateServer = false;
	protected static boolean serverInitiated = false;
	protected static boolean clientInitiated = false;
	protected static boolean anotherClient = false;
	//protected static boolean finishClients = false;
	protected static Integer clientCount = 0;

	public static void main(String args[]) throws InterruptedException
	{
		GUIFrame frame = new GUIFrame();
		frame.setDefaultCloseOperation( JFrame.EXIT_ON_CLOSE );
		frame.setSize( 1000, 600 );
		frame.setVisible( true);

		do  {
			if ( initiateServer ) {
			ServerThread st = new ServerThread(frame);
			Thread thread1 = new Thread(st);
        		thread1.start();
			serverInitiated = true;
                        Thread.sleep(500);
			};
		} while ( !serverInitiated );

	do {		

		do  {
			if ( connectClient ) {
			ClientThread ct = new ClientThread(frame);
			Thread thread2 = new Thread(ct);
			thread2.start();
			clientInitiated = true;
			connectClient = false;
			clientCount++;
                        Thread.sleep(500);
			};
		} while ( !clientInitiated );

		do  {
			Thread.sleep(500);
		} while ( !anotherClient );

	} while ( true );

    }
}
[/sourcecode]
