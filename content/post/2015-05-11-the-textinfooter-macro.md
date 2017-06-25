---
title: "The TextInFooter macro"
date: 2015-05-11 12:08:05+00:00
---

**This post is for my friend Sue and all the people that use Microsoft Word and want to add to the footer of their documents a reminder to the file location and the timestamp when the document was last saved. ****This can be achieved using a small VBA macro, and here is how to do it. **

Copy the content in the box below

    
    Sub FileSaveAs()
    
        Dialogs(wdDialogFileSaveAs).Show
        
        Dim i As Long
        Dim ThisPath As String
        Dim pName As String
        Dim TextInFooter As String
        Dim FullName As String
    
        ThisPath = ActiveDocument.Path
        pName = ActiveDocument.Name
        FullName = ThisPath & "\" & pName
        TextInFooter = "This file was saved in: " & FullName & " on the " & Now
    
        For i = 1 To ActiveDocument.Sections.Count
            With ActiveDocument.Sections(i)
                .Footers(wdHeaderFooterPrimary).Range.Text = TextInFooter
            End With
        Next
    End Sub


To save the macro:



	
  1. Open an existing word document, then press ALT+F11.

	
  2. Right-Click on Normal in the file explorer in the left hand side panel. Click on INSERT, then click on MODULE.

	
  3. Copy the text at the bottom of this email and paste it in the empty window that opens on the top-right panel.

	
  4. Click on SaveNormal.


If you want to save a document without changing the footer, you either press the save button or CTRL+S. These will not run the macro.

If you want to save a document with path+date&time in the footer, you either press FILE -> SaveAs button or F12. These will run the macro.

Remember, you have to save the macro only once. After that, the macro will work on any new or existing document on your computer. However if you change computer or reset it, you will need to re-save the macro.
