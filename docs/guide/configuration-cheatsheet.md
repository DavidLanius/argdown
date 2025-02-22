---
title: Configuration cheat sheet
meta:
  - name: description
    content: An overview over all current configuration options of the official Argdown tools.
---

# Configuration Cheat Sheet

The following typescript file contains all currently supported settings together with extensive comments on their meaning. For further information on most of these configuration options, please read the guide on [creating argument maps](/creating-argument-maps).

If you have questions about the syntax of the Cheat Sheet or how to use a configuration option in JSON, Yaml or Javascript syntax, please read the explanations [below the Cheat Sheet](#about-the-syntax-of-the-cheat-sheet).

```typescript
interface IArgdownConfig {
  /**
   * Document title. Required if you want
   * to create a HTML header
   * This and the following meta data settings
   * should be used directly in the frontmatter
   * section, not in a config file.
   **/
  title?: string;
  /**
   * subTitle, author, date and abstract are
   * optional meta data used for the HTML header
   **/
  subTitle?: string;
  /**
   * if there are several authors,
   * use an array: ["author 1", "author 2"]
   **/
  author?: string | string[];
  date?: string;
  abstract?: string;
  /**
   * The files that should be loaded
   * You can use a file glob.
   **/
  inputPath?: string;
  /**
   * Use this if you want to directly set a folder
   * and a file name for an exported file.
   *
   * Do not use this if you want the naming to be
   * done dynamically.
   **/
  outputPath?: string;
  /**
   * Will be appended to the file name of any files
   * exported.
   **/
  outputSuffix?: string;
  // settings for the data plugin
  data?: {
    /**
     * - If set to "ignore", any settings in the
     *   frontmatter will be ignored.
     * - If set to "default" or undefined the front
     *   matter yaml data settings are merged as
     *   default settings into the request object.
     * - If set to "priority" the yaml data settings
     *   overwrite any external settings.
     *
     * This makes it possible to configure plugins
     * without using an external argdown.config.js
     * file.
     */
    frontMatterSettingsMode?: "default" | "ignore" | "priority";
    /**
     * If false the YAML data of arguments,
     * statements and headings is always parsed with
     * the outer curly brackets.
     * In this case the YAML data has to always be
     * in inline format which looks similar to JSON
     * data.
     *
     * If true the data is parsed without the outer
     * curly brackets if the opening bracket is
     * followed by a line break.
     * This means that the YAML data has to be in
     * block format instead of the JSON-like inline
     * format.
     */
    switchToBlockFormatIfMultiline?: boolean;
  };
  // settings for the model plugin
  model?: {
    /**
     * if true, tags will be removed from statement
     * and description text
     **/
    removeTagsFromText?: boolean; // default: false
    /**
     * Determines how statement-to-statement
     * relations are to be interpreted:
     *
     * - In "loose" mode, - means attack and + means
     *   support.
     * - In "strict" mode, - means contrary and +
     *   means entails.
     **/
    mode?: "loose" | "strict"; // loose is default mode
    /**
     * If true, outgoing relations of reconstructed
     * arguments are transformed into outgoing
     * relations of the pcs's main conclusion.
     *
     * Incoming undercuts of reconstructed arguments
     * are transformed into incoming undercuts of
     * the pcs's last inference.
     **/
    transformArgumentRelations?: boolean; // default is true
  };
  // settings for the tag plugin
  color?: {
    /**
     * A custom color scheme or the name of a
     * built-in one
     *
     * colors have to be in #fffff hex format
     *
     * The color at index 0 will be used as default
     * color.
     *
     */
    colorScheme?: string[] | string; // default is "default"
    /***
     * A map that maps relation types to colors
     * Will be used for the edges in the dot/graphml
     * export and the vizjs/dagre maps
     * Relation types are:
     *
     * - attack
     * - support
     * - undercut
     * - contrary
     * - contradictory
     * - entails
     **/
    relationColors?: { [key: string]: string };
    /**
     * A custom color scheme for groups
     **/
    groupColorScheme?: string[];
    colorByTags?: boolean; // default is true
    colorGroupsByTags?: boolean; // default is false
    /**
     * Mapping of tags to colors.
     *
     * You can refer to a color scheme by index.
     *
     * You can give tags priorities to use them for
     * coloring even if they are not the first tags
     * applied.
     *
     * Example: {"tag-1": 1, "tag-2": "#CCCCCC",
     * "tag-3": {color: 0, priority: 2}}
     */
    tagColors?: {
      [tagName: string]:
        | string
        | number
        | { color: string | number; priority: number };
    };
    /**
     * Ignore color fields of data elements.
     **/
    ignoreColorData?: boolean; // default is false
    /**
     * A map from statement titles to colors
     *
     * Example: {"S1": 1, "S2": "#CCCCCC"}
     **/
    statementColors?: { [title: string]: string | number };
    /**
     * A map from argument titles to colors
     *
     * Example: {"A1": 1, "A2": "#CCCCCC"}
     **/
    argumentColors?: { [title: string]: string | number };
    /**
     * A map from section titles to colors
     *
     * Example: {"Heading 1": 1, "Heading 2": "#CCCCCC"}
     **/
    groupColors?: { [title: string]: string | number };
  };
  // settings for the selection plugins
  selection?: {
    /**
     * Can be used to only select arguments and statements with certain tags
     */
    selectedTags?: string[];
    /**
     * Should arguments and statements without tags
     * be excluded from the selection?
     * This is only relevant, if [
     * [ISelectionSettings.selectedTags]] is used.
     */
    selectElementsWithoutTag?: boolean;
    /**
     *
     * A list of headings that can be used to only
     * selected arguments and statements from
     * certain sections in the texts.
     */
    selectedSections?: string[];
    /**
     * Should arguments and statements that are
     * defined under no heading be excluded from the selection?
     * This is only relevant if [
     * [ISelectionSettings.selectedSections]] is
     * used.
     */
    selectElementsWithoutSection?: boolean;
    /**
     * Titles of statements that should be
     * represented as nodes in the map.
     *
     * This does not automatically exclude all other
     * statements from being put into the map as
     * nodes.
     *
     * It works similarly to using the isInMap:true
     * flag for the included statements.
     *
     * Which other statements are selected depends
     * on the other selection methods used.
     */
    includeStatements?: string[];
    /**
     * Titles of statements that should be excluded
     * from the map.
     */
    excludeStatements?: string[];
    /**
     * Titles of arguments that should not be in the
     * map.
     */
    excludeArguments?: string[];
    /**
     * If true, the isInMap data flag is ignored.
     */
    ignoreIsInMap?: boolean;
    /**
     * Should disconnected nodes be excluded from
     * the map?
     **/
    excludeDisconnected?: boolean; // default is true
    /**
     * The method by which preselected statements
     * are selected
     *
     **/
    statementSelectionMode?:
      | "all"
      | "with-title"
      | "with-relations"
      | "with-more-than-one-relation"
      | "not-used-in-argument"; // default: "with-title"
  };
  // settings for the map plugin
  map: {
    statementLabelMode?: "hide-untitled" | "title" | "text"; // default is "hide-untitled"
    argumentLabelMode?: "hide-untitled" | "title" | "text"; // default is "hide-untitled"
    addTags?: boolean; // should tags be added to the node labels?
  };
  // settings for the group and regroup plugins
  group: {
    /**
     * How many levels of groups should there be
     * derived from headings?
     * Derivation starts at the headings with the
     * highest section level and ends at
     * minGroupLevel = maxLevel + 1 - groupDepth
     * (section levels start at level 1)
     **/
    groupDepth?: number;
    /**
     * Alternative to using data flags for heading
     * elements in the Argdown document
     * This way you can use argdown.config.js files
     * with different section configurations
     * to define different groups or close different
     * sets of groups
     *
     * Example:
     *
     * ===
     * group:
     *  sections: {
     *    "Main arguments": {
     *        isGroup: true,
     *        isClosed: false
     *      },
     *    "Some peripheral arguments": {
     *        isGroup: true,
     *        isClosed: true
     *      },
     *    "Introduction": {
     *        isGroup: false
     *      }
     *  }
     * ===
     **/
    sections?: {[key:string]:{isClosed?:boolean; isGroup?:boolean}};
    /**
     * Can be used to override heading-based
     * grouping.
     *
     * Example:
     *
     * ===
     * group:
     *  regroup: [
     *  {
     *    title: "Group A",
     *    arguments: ["A1", "A2"],
     *    statements: ["S1"],
     *    children: [{
     *      title: "Group B",
     *      arguments: ["A3", "A4"],
     *      isClosed: true
     *    }]
     *  },
     *  {
     *    title: "Group C",
     *    arguments: ["A5", "A6"]
     *  }
     * ]
     * ===
     **/
    regroup?: [{
      title: string;
      tags?: string[];
      statements?: string[];
      arguments?: string[];
      isClosed?: boolean;
      children?: [];
    }[];
    /**
     * Should isGroup data flags in the document be
     * ignored
     **/
    ignoreIsGroup?:boolean;
    /**
     * Should isClosed data flags in the document be
     * ignored
     **/
    ignoreIsClosed?:boolean;
  }];
  // settings for the html export plugin
  html: {
    outputDir?: string; // default is "./html"
    /**
     * Remove sourrounding html and body tags,
     * remove head section of HTML.
     *
     * Instead a simple div containing the argdown
     * HTML is returned.
     */
    headless?: boolean;
    /**
     * If true, inserts a header with title
     * (required), subTitle, author, date and
     * abstract (if defined) from the frontMatter
     * section.
     **/
    createHeaderFromMetadata?: boolean; // default is true
    /**
     * External CSS file to include in the HTML head
     * section.
     */
    cssFile?: string;
    /** Title of the HTML document. If not provided,
     * the first top-level heading will be used. */
    title?: string;
    lang?: string;
    charset?: string;
    allowFileProtocol?: boolean;
    /** Optional setting to specify a custom head
     * section.
     **/
    head?: string;
    /** Function to test if a link is valid. */
    validateLink?: (url: string, allowFile: boolean) => boolean;
    /** Function to normalize links. */
    normalizeLink?: (url: string) => string;
    css?: string;
  };
  // settings for the json export plugin
  json: {
    outputDir?: string; // default is "./json"
    spaces?: number;
    /**
     * Should [[Argument.relations]], [
     * [Statement.relations]] be removed from the
     * JSON objects?
     */
    removeEmbeddedRelations?: boolean;
    /**
     * Should the JSON data include the response.map
     * property?
     */
    exportMap?: boolean;
    /**
     * Should the JSON data include sections?
     */
    exportSections?: boolean;
    /**
     * Should the JSON data include tag data?
     */
    exportTags?: boolean;
    /**
     * Should the JSON data include metaData?
     */
    exportData?: boolean;
  };
  // settings for the dot export plugin (and thus the VizJs map and the svg export)
  dot?: {
    outputDir?: string; // default is "./dot"
    graphname?: string;
    /***
     * Measure pixel width of each word and insert a
     * line break before or when line width reaches
     * lineWidth.
     *
     * Slower, but may be more exact.
     *
     * By default a line break is inserted before or
     * when the number of
     * characters reaches charactersInLine looking
     * for the nearest empty space.
     **/
    measureLineWidth?:boolean; // default is false
    /**
     * Background color of the whole map
     **/
    mapBgColor?:string; // default is "transparent"
    group?:{
      lineWidth?: number; // used if measureLineWidth is true
      charactersInLine?: number; // used if measureLineWidth is false
      /**
       * You can use "x,y" format for horizontal and vertical margins
       **/
      margin?:number; // default is "8"
      /**
       * Font to use
       *
       * Only a limited number of fonts are
       * supported,
       * see VizJs and string-pixel-width
       * documentation.
       *
       * VizJs: https://github.com/mdaines/viz.js/wiki/Caveats#Fonts
       *
       * string-pixel-width: https://github.com/adambisek/string-pixel-width
       **/
      font?:string;
      fontSize?:number;
      bold?:boolean;
    };
    /**
     * Settings for closed group state
     **/
    closedGroup?:{
      lineWidth?: number; // used if measureLineWidth is true
      charactersInLine?: number; // used if measureLineWidth is false
      /**
       * You can use "x,y" format for horizontal and vertical margins
       **/
      margin?:number; // default is "8"
      /**
       * Font to use
       *
       * Only a limited number of fonts are
       * supported,
       * see VizJs and string-pixel-width
       * documentation.
       *
       * VizJs: https://github.com/mdaines/viz.js/wiki/Caveats#Fonts
       *
       * string-pixel-width: https://github.com/adambisek/string-pixel-width
       **/
      font?:string;
      fontSize?:number;
      bold?:boolean;
    };
    argument?:{
      lineWidth?:number;
      /**
       * The minimum node width
       **/
      minWidth?:number;
      margin?:number; // default is "0.11,0.055"
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
    };
    statement?:{
      lineWidth?:number;
      /**
       * The minimum node width
       **/
      minWidth?:number;
      margin?:number; // default is "0.11,0.055"
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
    };
    /**
     * Any settings strings that are allowed in a
     * dot file.
     * The settings will be inserted in the
     * following form: 'key: "value";'
     *
     * Example: {
     *  rankdir: "BT",
     *  newrank: true
     * }
     */
    graphVizSettings?: { [name: string]: string };
  };
  // settings for the Dagre map (only relevant in the VSCode and sandbox previews)
  dagre?:{
    /**
     * Rank direction
     * BT, TB, LR, RL
     **/
    rankDir?: string; // default is BT
    /**
     * Rank separation
     **/
    rankSep?: number; // default is 50
    /**
     * Node separation
     **/
    nodeSep?: number; // default is 70
    measureLineWidth?:boolean; // default is false
    group?:{
      lineWidth?: number; // used if measureLineWidth is true
      charactersInLine?: number; // used if measureLineWidth is false
      /**
       * Font to use
       *
       * Only a limited number of fonts are
       * supported,
       * see string-pixel-width documentation.
       *
       * string-pixel-width: https://github.com/adambisek/string-pixel-width
       **/
      font?:string;
      fontSize?:number;
      bold?:boolean;
    };
    argument?:{
      lineWidth?:number;
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
    };
    statement?:{
      lineWidth?:number;
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
        charactersInLine?:number;
      };
    };
  };
  // settings for the graphml export plugin
  graphml?:{
    group?:{
      horizontalPadding?:number;
      verticalPadding?:number;
      width?: number; // used if measureLineWidth is true
      /**
       * Font to use
       *
       * Only a limited number of fonts are
       * supported,
       * see string-pixel-width documentation.
       *
       * For other fonts the width measurement will
       * fail,
       * so label text lines will not fit into nodes.
       *
       * string-pixel-width: https://github.com/adambisek/string-pixel-width
       **/
      font?:string;
      fontSize?:number;
      bold?:boolean;
    };
    argument?:{
      horizontalPadding?:number;
      verticalPadding?:number;
      width?:number;
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
      };
    };
    statement?:{
      horizontalPadding?:number;
      verticalPadding?:number;
      width?:number;
      title?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
      };
      text?:{
        font?:string;
        fontSize?:number;
        bold?:boolean;
      };
    };
  };
  // settings for the svg file export
  svg?: {
    outputDir?: string; // default is "./svg"
  };
  // settings for the VizJs map (used in the svg export and the VSCode and sandbox previews)
  vizJs?: {
    /**
     * Removes svg prolog (useful if svg is used
     * directly in html)
     **/
    removeProlog?: boolean;
    /**
     * Graphviz layout engine.
     * Available engines:
     * circo, dot, fdp, neato, osage, twopi
     **/
    engine?:string;
    /**
     * used for the neato engine
     **/
    nop?:number;
  };
  // outputDir for pdf file export
  pdf?: {
    outputDir?: string; // default is "./pdf"
  };
  // settings for inserting svg map into the pdf (SvgToPdfPlugin)
  svgToPdf?:{
    width?: number;
    height?: number;
    padding?: number;
    /**
     * Settings of pdfkit: http://pdfkit.org/
     **/
    pdf?: { compress?: boolean };
    /**
     * Settings of svg to pdfkit: https://github.com/alafr/SVG-to-PDFKit
     **/
    svg?: {
      useCSS?: boolean;
      assumePt?: boolean;
      preserveAspectRatio?: string;
    };
  }
  /**
   * If an array is used: the processors that should
   * be executed in order by the [[ArgdownApplication]]
   * during the current run.
   *
   * If a string is used: the name of the process to
   * be found in [[IArgdownRequest.processes]]
   * or a built-in process.
   */
  process?: string[] | string;
  /**
   * A dictionary of processes that can be run.
   *
   * Keys are the process names, values are list of
   * IArgdownConfig-like objects.
   *
   * There are only two differences to normal
   * IArgdownConfig objects:
   *
   * - the objects can not itself contain a
   *   processes field.
   * - the process field of the object has to be a
   *   name of a built-in-process or an array of
   *   processors to be run.
   */
  processes?: { [name: string]: IArgdownConfig };
  /**
   * Custom plugins that will be added before
   * running the current process.
   * They will be removed again afterwards.
   **/
  plugins?: [{ plugin: IArgdownPlugin; processor: string }];
  /**
   * Log level for the Argdown logger
   *
   * Set to "verbose" to get lots of information
   * about processors and plugins
   **/
  logLevel?: "none" | "error" | "warning" | "verbose"; // default is "error"
  /**
   * Should the application throw exceptions from
   * plugins?
   */
  throwExceptions?: boolean; // default is false
  /**
   * Should exceptions thrown by plugins be logged?
   */
  logExceptions?: boolean; // default is true
  /**
   * You can add a custom logger here
   **/
  logger?: IArgdownLogger;
}
```

## About the Syntax of the Cheat Sheet

In the Cheat Sheet Typescript is used to precisely define the types of the available configuration options.

The actual configuration in JSON, YAML or Javascript will look different. In the typescript interface definition, properties are separated by semicolons. In JSON, Javascript or YAML the properties will be separated by comma or simple linebreaks.

Typescript interface definition:

```typescript
interface MyInterface {
  property1?: Type1;
  property2?: Type2[];
}
```

Javascript implementation:

```javascript
{
  property1: value1,
  property2: [value2, value3]
}
```

JSON (and YAML inline format) implementation:

```json
{
  "property1": value1,
  "property2": [value2, value3]
}
```

YAML frontmatter implementation:

```yaml
===
property1: value1
property2:
  - value2
  - value3
===
```

In Typescript a `?` means that a property is optional.

In Typescript `option?: Type1 | Type2;` means that you can either use a value of `Type1` or of `Type2` for `option` or not use this setting at all.

In Typescript `option: string[];` means that you have to use an array of strings (`["string1", "string2"]` in JSON).

In Typescript `option: {[key:string]:string};` means that you have to use a map with string keys and string values. In JSON this looks like this:

```json
{
  "option": { "key1": "value1", "key2": "value2" }
}
```
