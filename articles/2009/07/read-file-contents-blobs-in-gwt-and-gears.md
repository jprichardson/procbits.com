<!--
author: JP
publish: Wed Jul 29 2009 21:24:01 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/07/29/read-file-contents-blobs-in-gwt-and-gears/
tags: GWT
slug: 2009/07/29/read-file-contents-blobs-in-gwt-and-gears
-->

Read File Contents (Blobs) in GWT and Gears
===========================================

What do you do if you want to read the contents of a file in your GWT
application? One possible solution is to use Google Gears.

Gears offers a method to allow a user to select file(s). You can then
use the returned "getFiles" method of the "OpenFilesEvent" object to
find out what file(s) the user selected. Each "File" class has a method
called "getBlob" that returns a "Blob" object. However, each blob object
doesn't provide any direct way to access the blobs contents. Many
[people](http://www.arcaner.com/2008/09/14/google-gears-blob-hate/)
[haven't](http://groups.google.com/group/gears-users/browse_thread/thread/0b4d3933a1ef0e91?fwc=1)
found a way to read the blob contents.

I wrote a class to access the contents of the blob. The class is called
"BlobReader" and is very simple to use. The trick lies in the fact that
in Javascript we can access the "getBytes" method of the blob.

Here is an example reading line by line of a text file:

    try {
        Desktop dt = Factory.getInstance().createDesktop();
        dt.openFiles(new OpenFilesHandler(){
            public void onOpenFiles(OpenFilesEvent event) {
                File[] files = event.getFiles();
                File file = files[0];
                Blob data = file.getBlob();

                BlobReader br = new BlobReader(data);
                while (!br.endOfBlob())
                    Window.alert(br.readLine());
                }
            }, true);
    } catch (Exception ex){
        Window.alert(ex.toString());
    }

Here is BlobReader.java:

    package com.yourpackage.client;
    import com.google.gwt.core.client.JsArrayInteger;
    import com.google.gwt.gears.client.blob.Blob;

    public class BlobReader {

        private final int CHUNK_SIZE = 1024;

        public BlobReader(Blob blob){
            this.blob = blob;
        }

        private Blob blob;
        public Blob getBlob(){ return this.blob; }

        private int blobPointer = 0;
        public int getBlobPointer(){ return this.blobPointer; }

        public boolean endOfBlob(){
            return (blobPointer >= blob.getLength());
        }

        public byte[] readAllBytes(){
            return getBlobBytes(this.blob);
        }

        public String readAllText(){
            int size = this.blob.getLength();
            StringBuilder sb = new StringBuilder(size);

            JsArrayInteger bytes = getBlobBytes(this.blob, 0, size);
            for (int x = 0; x < size; x++)
                sb.append((char)bytes.get(x));

            return sb.toString();
        }

        public String readLine(){
            int size = CHUNK_SIZE;
            boolean foundEndOfLine = false;

            JsArrayInteger bytes;
            StringBuilder sb = new StringBuilder(CHUNK_SIZE);

            while (!foundEndOfLine && size > 0){
                int dif = this.blob.getLength() - this.blobPointer; //remainder of the data
                if (dif < CHUNK_SIZE)
                    size = dif;
                bytes = getBlobBytes(this.blob, this.blobPointer, size);

                for (int x = 0; x < size; x++){
                    blobPointer++;
                    int b = bytes.get(x);
                    if (b == 10){ //newline
                        foundEndOfLine = true;
                        break;
                    }
                    sb.append((char)b);

                    if (this.getBlobPointer() % 1000000 == 0)
                        Window.alert("" + this.getBlobPointer());
                }
            }

            if (sb.substring(sb.length() - 1) == "\r")
                sb.deleteCharAt(sb.length() - 1);

            return sb.toString();
        }

           public void reset(){
            this.blobPointer = 0;
        }

        private byte[] getBlobBytes(Blob b){
            JsArrayInteger array = getBlobBytes(b, 0, b.getLength());
            byte[] bytes = new byte[array.length()];
            for (int x = 0; x < bytes.length; x++)
                bytes[x] = (byte) array.get(x); //in google docs we are told these are from 0-255

            return bytes;
        }

        private native JsArrayInteger getBlobBytes(Blob b, int offset, int length) /*-{
            return b.getBytes(offset, length);
        }-*/;
    }

Notes: 1) This hasn't been thoroughly tested/debugged. 2) At the time of
this writing, I'm using Gears 0.5 with the GWT-Gears 1.2.1. In the
current GWT-Gears svn trunk you can see upcoming support for the GWT
methods "getNativeBytes" and "getBytes"

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Enjoy!
