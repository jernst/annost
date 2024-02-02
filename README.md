## ANNOST: ANNOtate your Spec with Tests (ANNO-S-T).

### The problem: relating specifications to their tests

Imagine you have to write some tests for a protocol specification. Say, you want to test ActivityPub
or WebFinger or HTTP. These are standard protocols, so they are defined in standards documents
created by the W3C or the IETF or such.

Unless the standards organization has developed a conformance test suite, you somehow need to
translate the standards document into a set of tests that you can run against your code. How would
you go about that? (This also applies if you are/represent the standards organization yourself
and you try to define tests for your own standard.)

There are likely going to be dozens, maybe 100's of tests -- depending on the complexity of the
protocol -- that you need to define to get reasonable test coverage. Managing those is not 
simple, for several reasons:

* If you look at a given test you wrote (or worse, that somebody else wrote): how do you determine
  which part of the specification it claims to test? How do you easily review whether the test
  actually does a good job at testing what the spec says?

* If you read the specification, how do you know whether a test has been defined for a given
  paragraph, or perhaps many have been defined (which?), and whether they together constitute good
  test coverage for that paragraph? How do you go from a place in the spec to a specific file
  or function in your test suite?

* Many standards documents repeat themselves a bit. E.g. they may state the same requirements once
  in the introduction, and then later in another section. How do you make sure you don't create basically
  the same test multiple times, or not at all because you thought you covered it "over there"?
  That is a real problem for long standards.

* Standards evolve. If you compare the newer version with the old version of the specification,
  how do you make corresponding changes to the tests in your test suite? Which tests remain unchanged,
  which need to be changed or dropped, which new text requires new tests?

### The solution: if you are very diligent and have a lot of time ...

... you are going to come up with some kind of spreadsheet, or such, and review the connections
between specs and tests so often that you can definitely manage. 

But then, you are not like me.

### The solution: if you are like me, and want to "see" that connection between specs and tests more directly ...

... then you use ANNOST: hack the spec document by inserting your tests right into the spec.

Sounds atrocious? Yep, it is. But also surprisingly useful and usable.

### Example

This is from work marking up the ActivityPub spec for [feditest.org](https://feditest.org/):

Section 6.10 of the unmodified spec:

![Section 6.10 -- unmodified](images/ap-spec-example.png)

Section 6.10 of the spec with a test and a note ANNOST annotation:

![Section 6.10 -- with ANNOST annotations](images/ap-spec-example-annost.png)

Other than adding a Javascript include into the HTML header, the boxes were created by the
following added markup :

<pre>
&lt;annost-test testid="6.10/1" name="Only the same actor may undo" role="FP-endpoint, C-endpoint" level="MUST" id="test-6.10/1"&gt;
Any UNDO activity created by a client or FP endpoint MUST have the same actor
as the activity referenced that is being undone.
&lt;/annost-test&gt;
</pre>
<pre>
&lt;annost-note&gt;
Some sensible ideas, but all is optional, so nothing to test.
&lt;/annost-note&gt;
</pre>
(You may disagree with the test definition and the note with respect to testing ActivityPub
-- this is very much work in progress and not the point here.)

### How does it work?

You need to work with a spec whose format is HTML, because we are using HTML trickery (custom
HTML tags / [Web Components](https://en.wikipedia.org/wiki/Web_Components).

1. Make a copy of the spec, such as `complicated-standard.html` to `complicated-standard-annost.html`.
 
1. Open the annosted HTML file in a browser window.

1. Add Annost: edit `complicated-standard-annost.html` and this single line to the HTML HEAD:

   <pre>&lt;script src="annost.js"&gt;&lt;/script&gt;</pre>

1. Open the annosted HTML file in an editor. As you keep reading through the spec in your browser,
   every time you think, "hmm, this might be a test", you find the corresponding place in the
   HTML in your editor, and you add an ANNOST web component. Currently you have three to choose
   from:

   * <pre>&lt;annost-test testid="SECTION/INDEX" name="NAME" role="ROLE" level="LEVEL">
     LONG DESCRIPTION...
     </pre>

     This defines a test. You give it a `testid` derived from the SECTION of the spec in which 
     you define this test, and another local identifier (INDEX) in case you have several tests
     in the same section. NAME is a short name so you can remember what it is. ROLE indicates
     whether this tests the client, or the server, or whatever other roles you have in your
     protocol, and LEVEL is something like "must" or "should". LONG DESCRIPTION: you provide
     as much detail as needed so the test can be coded.

     When you code the test, you name it based on SECTION/INDEX and/or NAME, so it is easy
     to go from test to spec (we know the section in which it was defined) and from spec
     to test (it's named after the section, and the content of the above tags is inlined
     right in your browser in the context of the spec.

   * <pre>&lt;annost-xref target="TARGETID"&gt;&lt;annost-xref&gt;

     Use this to create a cross-reference to another test. This is useful if the spec describes
     something testable for which you defined a test before. This way, you can remind yourself:
     yes, I dealt with this before. (Sorry, no backreferences yet.)

   * <pre>&lt;annost-note&gt;TEXT&lt/annost-note&gt;

     This lets you leave a note. Like "I have no idea what they are talking about here" :-)

1. Yep, it's a crude process, and you might inadvertently change the text of the standard,
   not just add ANNOST annotations. So every now and then, you might want to do
   `diff complicated-standard.html complicated-standard-annost.html`. If you are reasonably
   careful about your editing, all it should show is ANNOST blocks. And if not, it's easy
   enough to fix the annoated copy.



