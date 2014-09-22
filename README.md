DC Code XML
===========

This repository contains the Code of the District of Columbia in XML format.

The DC Code in XML makes use of a custom schema that draws on ideas from [Akoma Ntoso](http://www.akomantoso.org/) and [United States Legislative Markup](http://uscodebeta.house.gov/download/download.shtml).

The schema can almost entirely be seen in the following four examples.

(1) Root element. The root element is a `<level>` element. It contains some metadata and nested `<level>` elements for the first level divisions of the code.

	<level type="document">
		<heading>Code of the District of Columbia</heading>

		<meta>
			<recency>
				<last-act>DC Act 20-101</last-act>
				<effective-date>2014-09-09</effective-date>
				<publication-date>2014-09-24T17:03:41-04:00</publication-date>
			</recency>
		</meta>

		<!-- <level> elements as shown below -->

	</level>

(2) "Big" levels that appear in the Table of Contents (`toc`). These are divisions, titles, chapters, and so on above the level of a section.

    <level type="toc">
		<prefix>Title</prefix>
		<num>1</num>
		<heading>Government Organization</heading>

		<!-- other <level> elements nested here -->

    </level>

(3) Sections and annotations.

	<level type="section">
		<num>2-531</num>
		<heading>Public policy.</heading>

		<text>The public policy of the District of Columbia is that all persons are entitled
		to full and complete information regarding the affairs of government and the official
		acts of those who represent them as public officials and employees. To that end,
		provisions of this subchapter shall be construed with the view toward expansion of public
		access and the minimization of costs and time delays to persons requesting information.</text>

		<level type="annotations">
		  <level>
		    <heading>History</heading>
		    <text>(Oct. 21, 1968, Pub. L. 90-614, title II, ...</text>
		  </level>
		</level>
	</level>

(4) "Little" levels which are rendered as paragraphs (often numbered).

	<level>
		<num>(c)</num>
		<heading>United States</heading>

		<text>The United States, its officers, employees, and agents, and its agencies shall not:</text>

		<level>
			<num>(1)</num>
			<text>Be responsible for the payment of any judgments, liabilities or costs resulting
			from any action or proceeding against the District of Columbia or its agencies,
			officers, employees, or agents;</text>
		</level>

		<!-- other <level> elements nested here -->
	</level>

XIncludes
---------

The Code is one logical XML document (approximately 75 megabytes), but it has been broken apart into approximately 26,000 smaller files to make it more manageable. The Code begins in [index.xml](index.xml) and [XInclude](http://www.w3.org/TR/xinclude/) tags stitch all of the documents together. All XIncludes target `<level>` elements at or above the section level. We have split the code at titles, chapters, subchapters, and so on down to sections.

Each XInclude element has an `href` attribute with a relative path to the file that is logically included at that point.

The top-level `index.xml` file looks like this:

	<level type="document">
	  <heading>Code of the District of Columbia</heading>
	  <meta>
	    ...
	  </meta>
	  <level type="toc">
	    <prefix>Division</prefix>
	    <num>I</num>
	    <heading>Government of District</heading>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-1/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-2/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-3/index.xml"/>
	    ...
	  </level>
	  <level type="toc">
	    <prefix>Division</prefix>
	    <num>II</num>
	    <heading>Judiciary and Judicial Procedure</heading>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-11/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-12/index.xml"/>
	    ...
	</level>

The namespace prefix `ns0` is subject to change.

Document Element
----------------

The root element is a `<level>`, like most other nodes in the schema, with `type="document"`. We are not using namespaces.

This document element contains a `<meta>` element with document metadata.

The `<meta>` element is followed by `<level>` elements representing the Divisions of the Code. In the actual root file `index.xml` in this repository some of the `<level>` elements shown below are replaced by XIncludes, but we show them replaced with their referenced content here to highlight the intended semantics.

	<?xml version='1.0' encoding='utf-8'?>
	<level type="document">

	  <heading>Code of the District of Columbia</heading>

	  <meta>
	    ...
	  </meta>

	  <level type="toc">
	    <prefix>Division</prefix>
	    <num>I</num>
	    <heading>Government of District</heading>

	    <level type="toc">
	      <prefix>Title</prefix>
	      <num>1</num>
	      <heading>Government Organization</heading>

	      <level type="toc">
	        <prefix>Chapter</prefix>
	        <num>1</num>
	        <heading>District of Columbia Government Development</heading>

	        <level type="toc">
	          <prefix>Subchapter</prefix>
	          <num>I</num>
	          <heading>District of Columbia Establishment</heading>

	          <level type="section">
	            <num>1-101</num>
	            <heading>Territorial area.</heading>
	            <text>The District of Columbia is that portion of the territory
	            of the United States ceded by the State of Maryland for the
	            permanent seat of government of the United States, including the
	            river Potomac in its course through the District, and the islands
	            therein.</text>

Metadata
--------

The document's metadata describes the recency of the DC Code publication:

	<recency>
	  <last-act>DC Act 20-101</last-act>
	  <effective-date>2014-09-09</effective-date>
	  <publication-date>2014-09-24T17:03:41-04:00</publication-date>
	</recency>


The `last-act` field is a textual field describing, in human-readable form, the last act that has been codified in the document. For corresponding machine-readable data, see the file `update-chart.csv` at the root of the repository.

The `effective-date` field contains an ISO 8601-formatted date corresponding to the effective date of the most recently codified act. The DC Code is expected to accurately reflect the law on this date.

The `publication-date` field contains an ISO 8601-formatted date indicating when the document was published by the DC Council. The publication date may be before or after the effective date.

Levels
------

All hierarchy of the Code is represented by `<level>` elements. This includes both "big" levels (titles, chapters, etc.), sections, and "little" levels (subsections, paragraphs, and so on).

### Type Attribute

The `type` attribute indicates the type of the level, as follows:

* `document`: The root of the entire document. There is one such element and it is at the top of the hierarchy.
* `toc`: These are divisions, titles, chapters, and so on above the level of a section. `toc` is a mnemonic for Table of Contents. These levels appear in the Table of Contents of the code.
* `section`: Sections of the code (e.g. § 1-101), the primary level at which text and annotations occur and what citations target.
* `placeholder`: The level is section-like but it represents Code sections that no longer exist. They are documented specifically in a section below.
* `annotations`: Annotation blocks.
* `appendices`: Appendices to a section (see [Title-16/Chapter-9/16-916.01.xml](Title-16/Chapter-9/16-916.01.xml)).
* `form`: Blocks that are rendered with internal whitespace preserved. These elements contains quoted language that the Code is directing to be used on government forms.
* When `type` is omitted, the level is a numbered paragraph (i.e. `(1) The public policy....`) or an unnumbered block with a heading. Inside annotations, subheadings are represented this way.

### Level Numbering

A `<level>` element must contain at least one of `<prefix>`, `<num>`, or `<heading>`.

`<prefix>` is used only in `type="toc"` levels to provide the text that appears before the level's number.

* The inner text of this element is one of `Article`, `Chapter`, `Division`, `Part`, `Subchapter`, `Subdivision`, `Subpart`, `Subtitle`, `Title`, and `Unit`.

`<num>` indicates the level's number.

* For `toc` levels, this usually is a number, letter, or roman numeral.
* For sections, the number is roughly in the form `1-101` (title, hyphen, section number). It never includes the §-symbol.
* For "little" levels, this is the paragraph's numbering including any parenthesis around the numbering. An example would be `(12A-i)`.
* When `<num>` is omitted, the level is simply unnumbered. `document` and `placeholder` levels never have `<num>` elements. For placeholders, see below.

`<heading>` contains the level's name or heading.

* For `toc` levels, sections, placeholders, and subheadings in annotations, this is the level's title or heading, such as `Government Organization`.
* A heading can also appear on "little" levels and would appear as something like italicized text.

### Content Elements

Following the `<level>`'s numbering elements described just above, a `<level>` must contain one or more `<level>` or `<text>` (see below) elements.

* These elements must be rendered in document order.
* For "little" levels, the level's `<num>` and/or `<heading>` are typically rendered inside and at the start of the first `<text>` paragraph.

Text
----

Text content is contained within `<text>` elements inside `<level>`s. 

* Each `<text>` element is a separate paragraph.
* `<text>` may be interleaved with other `<text>` and `<level>` elements and must be rendered in document order.
* `<text>` elements contain a subset of HTML.

This is an example of a typical `<text>` element:

	<text>One <span style="font-style: italic; ">ex officio </span>Commissioner,
	the Deputy Mayor for Planning and Economic Development;</text>

An optional `class` attribute can be one of:

* `centered`: The paragraph is rendered with centered text alignment.

Inside the `<text>` element is HTML conforming to the following restrictions:

* Only bare text, `<span>`, and `<table>` elements may appear.
* `<span>` elements may have a style attribute containing only the following CSS:
 * `font-weight: bold;`
 * `font-style: italic;`
 * `text-decoration: underline;`
 * `font-family: monospace;`
* `<table>` elements may contain `thead`, `tr`, `th`, and `td` elements. `th` and `td` elements may have `colspan` attributes (as in HTML), and their content is restricted to the same subset of HTML as for `<text>` elements. When a `<table>` element appears inside a `<text>` node, it is the only content in the the `<text>` node (because each `<text>` node represents a paragraph).

Placeholders
------------

Placeholder levels are similar to levels with `type="section"` but represent Code sections that have been repealed, omitted, etc. Here is an example:

	<level type="placeholder">
	  <reason>Not funded</reason>
	  <section>1-136.01</section>
	  <heading>Establishment of the 51st State Commission.</heading>
	  <text>[Not funded].</text>
	  <level type="annotations">
	    ...

These levels do not have a `<num>` element. The `<heading>` element typically contains the name of the section that was removed. These levels may also have these other child elements:

* `<reason>`: The reason for the placeholder.
 * When present, one of `Expired`, `Not funded`, `Omitted`, `Repealed`, `Reserved`, and `Transferred`.
 * When not present, the reason for the placeholder is not indicated in the code.
* `<section>`: The number of the omitted section (e.g. `1-136.01`).

In place of `<section>`, a placeholder may instead contain:

* `<section-start>`: The number of the first omitted section in a range of omitted sections.
* `<section-end>`: The number of the last omitted section in a range of omitted sections.
* `<section-range-type>`: This element contains the text `range` if all sections between start and end are omitted (usually rendered as "A to B") or `list` if the start and end name two sections but a range is not implied (usually rendered as "A, B").

