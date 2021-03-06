# mathlatex
<!--Category Definitions-->
[[Category:TeX]]
[[Category:Math display extensions]]
This article describes the MathLaTeX Mediawiki[http://www.mediawiki.org] Extension that renders mathematical equations on wiki pages using a local LaTeX[http://www.latex-project.org] installed with Cygwin[https://www.cygwin.com].

This article was written on January 8, 2016 and is best viewed at 100%.<br />
Document version 1.00<br />
Software version 1.20<br />
Copyright (C) 2015 Jesse B. Dooley under GNU v.3[http://www.gnu.org/licenses/]

=Preamble=
The problem is how to render a mathematical equation in a MediaWiki article in as efficient and effective a manner as possible with the least aggravation. 

The solution is simple. Render the equation as an image and place it in the article inside a Image Tag[http://www.mediawiki.org/wiki/Help:Images]. The complexity comes from how many different ways there are to render an equation into an image multiplied by how many ways each solution can be implemented.

There are five possible rendering solutions, HTML, LaTeX, MathJax, MathML, and Mathoid. Each can be implemented in two ways, render locally or with a remote server. Consider four popular OSs; Linux, MacOS, OpenBSD, and Windows. This gives 20 different solutions. Cramming all possible solutions into one extension violates the maxim ''One size never fits all'' and is a quick way to a hot mess. A better architecture choice is to publish a framework solution and let the MediaWiki community implement solutions. 

The MathLaTeX extension is my effort at implementing the solution for LaTeX rendering under Windows 7 Ultimate x64 SP1 with Cygwin x64. 

Tested Configuration:<br />
Cygwin64<br />
Mediawiki 1.24.2<br />
XAMPP 5.5.24<br />
Windows 7 Ultimate x64 SP1<br />

=Installation=
* Note: This installation works for me. Your performance will vary.
* Note: This assumes only the C drive. Change your installation as needed.
* Note: Do not install the documentation for Tex Live[https://www.tug.org/texlive/]. This can add hours to the installation time and yields nothing useful.

==Cygwin==
Install Cygwin 64bit along with ghostscript, ImageMagick and latex/texlive.

'''Install the following Cygwin packages.'''<br />
ImageMagick<br />
ghostscript<br />
ghostscript-fonts-other<br />
ghostscript-fonts-std<br />
poppler-data<br />
popt<br />
popt-devel<br />
texlive<br />
texlive-collection-basic<br />
texlive-collection-bibtexextra<br />
texlive-collection-binextra<br />
texlive-collection-context<br />
texlive-collection-fontsextra<br />
texlive-collection-fontsrecommended<br />
texlive-collection-fontutils<br />
texlive-collection-formatsextra<br />
texlive-collection-genericrecommended<br />
texlive-collection-langenglish<br />
texlive-collection-langfrench<br />
texlive-collection-langgerman<br />
texlive-collection-langgreek<br />
texlive-collection-langspanish<br />
texlive-collection-latex<br />
texlive-collection-latexextra<br />
texlive-collection-latexrecommended<br />
texlive-collection-mathextra<br />
texlive-collection-metapost<br />
texlive-collection-music<br />
texlive-collection-omega<br />
texlive-collection-pictures<br />
texlive-collection-plainextra<br />
texlive-collection-pstricks<br />
texlive-collection-publishers<br />
texlive-collection-science<br />
texlive-collection-xetex<br />

'''Add these to the Windows System Path in this order:'''<br />
C:\cygwin64\usr\local\bin;<br />
C:\cygwin64\usr\bin;<br />
C:\cygwin64\bin;<br />
C:\cygwin64\lib;<br />

'''Create these Windows System Variables:'''<br />
CYGWIN	=	nodosfilewarning<br />
HOME	=	/cygdrive/c/cygwin64/home/<User Name><br />

==MathLaTeX==
'''Make these changes to LocalSettings.php'''<br />
// ImageMagick Settings - using C:/cygwin64 64bit<br />
$wgUseImageMagick = true;<br />
$wgImageMagickConvertCommand = "C:/cygwin64/bin/convert.exe";<br />

// Path to the GNU diff3 utility. Used for conflict resolution.<br />
$wgDiff  = "C:/cygwin64/bin/diff.exe";<br />
$wgDiff3 = "C:/cygwin64/bin/diff3.exe";<br />

// MathLaTeX<br />
require_once "{$IP}/extensions/MathLaTeX/MathLaTeX.php";<br />
require_once "{$IP}/extensions/MathLaTeX/SpecialMathLaTeX.php";<br />

===Buttons===
[[Image:button_mathlatex_large.png| 180px | thumb | left | Fig. 1 MathLaTeX Button]]

The edit button looks like pi-in-a-circle [[Image:button_mathlatex_classic.png]] and loads into the Classic Edit Toolbar with MathLaTeX.

The edit button for WikiEditor looks like pi-in-a-circle [[Image:button_mathlatex_wikieditor.png]].

To load the WikiEditor button put the following code into your MediaWiki:Common.js article and add '''$wgUseSiteJs = true;''' to LocalSettings.php.
<br clear=all>
 <nowiki>
var customizeToolbar = function() {
	$('#wpTextbox1').wikiEditor('addToToolbar', {
	section: 'main',
	group: 'format',
	tools: {
		"mathlatex": {
			label: 'MathLaTeX',
			type: 'button',
			icon: 'http://localhost/<wiki name>/extensions/MathLaTeX/images/button_mathlatex_wikieditor.png',
			action: {
				type: 'encapsulate',
				options: {
					pre:"&lt;mathlatex width= height= dpi= &gt;",
					post:"&lt;/mathlatex&gt;"
				}
			}
		}
	}
});

};

/* Check if view is in edit mode and that the required modules are available. Then, customize the toolbar … */
if ( $.inArray( mw.config.get( 'wgAction' ), [ 'edit', 'submit' ] ) !== -1 ) {
	mw.loader.using( 'user.options', function () {
		// This can be the string "0" if the user disabled the preference ([[phab:T54542#555387]])
		if ( mw.user.options.get( 'usebetatoolbar' ) == 1 ) {
			$.when(
				mw.loader.using( 'ext.wikiEditor.toolbar' ), $.ready
			).then( customizeToolbar );
		}
	} );
}
// Add the customizations to LiquidThreads' edit toolbar, if available
mw.hook( 'ext.lqt.textareaCreated' ).add( customizeToolbar );
</nowiki>

===Service Account===
The service account 'MW_MATH' is used for all the files and pages produced by MathLaTeX. On a public wiki grant MW_MATH administrative rights so the Special Pages can be updated.

==Testing==
The <tt>README</tt> contains this article in wiki text. Create a new article in your wiki, copy and paste the <tt>README</tt> into it and save. If no error messages are produced the installation was successful.

=User Manual=
MathLaTeX renders LaTeX statements as Portable Network Graphics[https://en.wikipedia.org/wiki/Portable_Network_Graphics] (PNG) files, then replaces the LaTeX statement in the WikiText with an Image Tag for that PNG file. Expressing an equation as a LaTeX statement is beyond this article's scope but two places to start are, ''Mediawiki's Help:Displaying a formula''[https://meta.wikimedia.org/wiki/Help:Displaying_a_formula] and ''Online LaTeX Editor''[http://www.codecogs.com/latex/eqneditor.php].

Place the LaTeX statement between the MathLaTeX tags. For example, E=mc^{2} would be &lt;mathlatex&gt;E=mc^{2}&lt;/mathlatex&gt; and is rendered as, <mathlatex>E=mc{2}</mathlatex>. MathLaTeX displays error messages in the wiki article starting where the MathLaTeX tag was.

LaTeX documentation is at the LaTeX Project[http://latex-project.org/]. 

==Attributes==
The opening tag takes three optional attributes, width, height, and dpi. Each takes an integer argument. Width and height are expressed in pixels and control how the image is displayed inside the Image tag. DPI, Dots-Per-Inch, controls the rendered image size. 

Table 1 illustrates different DPI settings. A large DPI produces a larger image. A smaller DPI produces a smaller image. The default DPI is 120 because it produces an image easily embedded in text.
{| class="wikitable"
!colspan="3"| Table 1. Equation at different DPI
|-
!TeX
!Image
!DPI
|-
|&lt;mathlatex &gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex>E=mc{2}</mathlatex> 
|120
|-
|&lt;mathlatex dpi=200 &gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex dpi=200>E=mc{2}</mathlatex>
|200
|-
||&lt;mathlatex dpi=300&gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex dpi=300>E=mc{2}</mathlatex>
|300
|}
Different Width and Height are applied in Table 2 below using the same 120 DPI image.
{| class="wikitable"
!colspan="3"| Table 2. Width and Height. 120 DPI
|-
!TeX
!Image
!Width/Height
|-
|&lt;mathlatex width=100 height=200&gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex width=100 height=200>E=mc{2}</mathlatex>
|100/200
|-
|&lt;mathlatex width=200 height=100&gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex width=200 height=100>E=mc{2}</mathlatex>
|200/100
|-
|&lt;mathlatex width=100&gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex width=100>E=mc{2}</mathlatex>
|100/
|-
||&lt;mathlatex height=100&gt;E=mc{2}&lt;/mathlatex&gt;
|<mathlatex height=100>E=mc{2}</mathlatex>
|/100
|}
Attributes are entered in the opening tag. For example. Write E=mc^{2} in an article. Highlight it. Then click the edit button to produce this: &lt;mathlatex width= height= dpi= &gt;E=mc^{2}&lt;/mathlatex&gt;. An unset attribute is ignored. A set one is processed.

==Configuration==
The variable $NamespaceWhiteList lists Namespaces[https://www.mediawiki.org/wiki/Help:Namespaces], by id, that MathLaTeX will not operate in. By default MathLaTeX only operates in Main.<br />
The constant DEFAULT_DPI defines the default Dots-Per-Inch used. The default is 120.
===Configuration Files===
The configuration files are stored in <tt>MathLaTeX/config/</tt>.
* latexpackages.php holds two arrays, $requiredlibs and $seachlibs. The $requiredlibs holds Cygwin packages required by Render::wrapper. The $seachlibs array holds Cygwin packages required by MathLaTeX.
==Directory Structure==
Under the <tt>MathLaTeX</tt> directory there are:
* <tt>config</tt> - holds the configuration files
* <tt>i18n</tt> - holds the localisation files.
* <tt>images</tt> - holds the images
* <tt>maintenance</tt> - holds the maintenance scripts
* <tt>modules</tt> - holds the button javascript file.

==Debugging==
On a failure a stack-trace is written into the article starting at the equation position.

The wfDebugLog function is used throughout MathLaTeX so enable $wgDebugLogFile[https://www.mediawiki.org/wiki/Manual:How_to_debug] for more detailed debugging.

The variable $MathDebug controls whether the temporary folder used for rendering is deleted. Set to TRUE and it is not deleted. Set to FALSE and it is deleted. The default setting is FALSE.

==Error Messages==
When an error occurs the MathLaTeX statement is replaced with an error message that does a stack trace. In the Error Message below dvipng.exe is inaccessible. Note that the original equation is reproduced in line 4.

<span style="color:red">MathLaTeX Error begin</span><br />
<span style="color:red">MathLaTeX::body::onPageContentSave createImage</span> failed<br />
Equation begin<br />
e=mc3<br />
Equation end<br />
<span style="color:red">MathLaTeX::body::createImage render</span> failed<br />
<span style="color:red">Render:DviPNGrender</span> failed<br />
<span style="color:red">Render::DviPNGrender png creation</span> failed<br />
cmd dvipng.exe -bg Transparent --gamma 1.5 -D 120 -T tight --strict MW_MATH_5c59dc24ade841a750bd5849dec7ea12_120.dvi -o MW_MATH_5c59dc24ade841a750bd5849dec7ea12_120.png<br />
retval 1<br />
dvipng result <br />
<span style="color:red">MathLaTeX Error end</span>

The [[MathLaTeX_Documentation#Special_Pages | Special Pages]] throws an MWException upon error.

==Maintenance Scripts==
In the <tt>maintenance</tt> subdirectory the <tt>equationCensus.php</tt> will list all the MW_MATH images and how many pages each image is linked to. An image with one link is only displayed in the Gallery and is a candidate for deletion.

==Render==
The MathLaTeXRender process wraps the author's equation statement to produce a valid LaTeX statement for processing. The default wrapper is shown in Table 3.
{| class="wikitable" style="float:left; margin-left: 
!Table 3. Default LaTeX Wrapper
|-
|
\nonstopmode<br />
\documentclass[12pt]{article}<br />
\usepackage{mathtools}<br />
\usepackage{lmodern}<br />
\usepackage{amsmath}<br />
\usepackage{amsfonts}<br />
\usepackage{amssymb}<br />
\usepackage{pst-plot}<br />
\usepackage{color}<br />
\pagestyle{empty}<br />
\begin{document}<br />
$$<br />
Equation Text Here.<br />
$$<br />
\end{document}<br />
|}
To specify a custom wrapper begin the statement with a "%" as the first character. LaTeX uses the "%" character to start comment lines. MathLaTeXRender will then bypass the default wrapper and send the LaTeX statement on to texlive.
<br clear=all>
==Repository==
The MatLaTeXRepository process saves files using "MATH_MW" as the owner in the Mediawiki File Repository. All file operations act only on files with "MATH_MW" as the owner. The LaTeX statement for an image is saved in the Summary section. The Summary is limited to 200 characters, so only the first 200 characters are saved.

==Special Pages==
Visibility into MathLaTeX on a private wiki is trivial. Visibility into MathLaTeX on a public wiki is gets a bit more complex. Did the Sysop modify the default wrapper? Were changes made to the default texlive packages? Is the equation I want already made?

Three Special pages, Gallery, Packages, and Wrapper, answer these questions.

Open the '''MathLaTeX''' button on ''Special pages'' displays the menu for the three Special pages as seen below:
[[File:MathLaTeX_Special.png |left| 300px |thumb| Fig. 2 MathLaTeX Special Menu ]] Each button updates the associated page. The link to the right displays that page. Only a WikiSysOp can update the Special pages. Each page should be posted as read-only on the site's MathLaTeX page.
<br clear=all>

===Gallery===
[[File:MW_MATH_GALLERY_sample.png|left| 500px |thumb| Fig. 3 MathLaTeX Gallery Sample ]] Each equation is listed in descending file-name alphabetical order. Each file-name is enclosed inside an image tag for and easy cut-and-paste.
<br clear=all>

===Packages===
[[File:MW_MATH_Packages_sample.png|left| 200px |thumb| Fig. 4 MathLaTeX Packages Sample ]] <pre style="color: green">Required packages are in Green.</pre> <pre style="color: red">Missing required packages are in Red.</pre>
 Extra packages are in Black.
<br clear=all>

===Wrapper===
[[File:MW_MATH_Wrapper_sample.png|left| 300px |thumb| Fig. 5 MathLaTeX Wrapper Sample ]] 
The default latex wrapper is displayed at left. The user's latex statement replaces ''Equation Text Here''. If the user's latex statement starts with a '%' then the entire default latex statement is replaces with the user's.
<br clear=all>

=Architecture=
Rendering a TeX statement as an image in Mediawiki[https://www.mediawiki.org/wiki/MediaWiki] presents three major problems:
Rendering in LaTeX, here implemented as Tex Live, is a CPU hog that inhales cycles. An equation image can be displayed using a separate image server or using the Images[https://www.mediawiki.org/wiki/Help:Images] tag. Error Messages can be displayed to the user as an image, a popup window, or text.

Maximizing an efficient and simple solution resulted in the following design choices:
# Maximize image reuse
## Standardize the image naming scheme for efficient image lookup and reuse.
## Provide an image gallery that is automatically updated.
# Use the Image tag because it is more efficient than building a separate image server.
# Use a standardized error message in text format to replace the MathLaTex statement in the wiki page.

==Flowchart==
[[Image:MathLaTeX_Flowchart_v1.jpg | 180px | thumb | left | Fig. 6 MathLaTeX Flowchart]]
<br clear=all>
 <nowiki>
-- Flowchart in text format
Start -> onPageContentSave -> 
if namespace is allowed continue, else return true
->
if text has mathlatex label, continue, else return true
->
if >0 valid equations, continue, else return true
->
foreach equations
  -> trimplaintext
     -> if length == 0 skip else continue
  -> create equation filename
  -> search repository for filename
     -> if not found 
      createImage
        -> if success return true, add to repository, else return error message
        -> if createImage fails replace equation in wiki page with error message, continue
  -> replace equation in wiki page with equation filename
End
</nowiki>

==Rendering==
The MathLaTeXRender class performs the following operations on the latex statement:
# Wraps the latex statement in the default latex wrapper.
## Do not wrap the latex statement if the first character is '%'.
# Run 'pdftex.exe --fmt=latex --interaction=nonstopmode <latex statement>' to produce a DVI file.
# Run 'dvipng.exe -bg Transparent --gamma 1.5 -T tight --strict <DVI file>' to produce the PNG file.
This process produces the sharpest image over a wide size range.

==Storage==
Why not add tables to the wikidb and fix the Summary Bug? Because adding random tables to a database design is egregiously bad engineering that could corrupt the database now or sometime in the future. Another possibility is that a future wikidb design will eliminate user added tables. 

For future consideration. A separate MySQL database to store equation data.

=Bug List=
* 001 Summary Bug - Two hundred character limit for the saved latex statement.

=Future Features=
* Write a hook to combine needed features from onPageContentSave and ParserFirstCallInit. Maybe call it ParseFirstCallPageSave.
* Link images into a group.

=Version=
1.0 Initial Release<br />
1.1 Fixed attribute parser bug.<br />
1.2 Cosmetic changes to the documentation.<br />
