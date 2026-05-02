# 📝 Short Answers
**File:** `short-answers.md`  
**Author:** Johnson Wuraola | **Assessment:** Worknoon WordPress Developer Assessment

---

## Q1. Difference Between Google Knowledge Graph and Google Knowledge Panel

These two terms are closely related but refer to different things one is the **database**, the other is the **display**.

### Google Knowledge Graph
The **Knowledge Graph** is Google's internal database of entities(people, organizations, places..) and the relationships between them. it is somewhat an interconnected map of real-world things.

### Google Knowledge Panel
The **Knowledge Panel** is the **visible user interface element** that Google displays on the right side of search results when a user searches for a well-known entity. It is powered by data pulled from the Knowledge Graph.


## Q2. How Google Determines Entity Identity

Google uses a process called **entity resolution**  determining whether a name, website, or signal across the web refers to the same real-world thing. This is how Google decides *"Johnson Wuraola the web developer and the person mentioned on GitHub are the same entity."*


Google establishes entity identity through **consistency + authority + explicitness**.

**Consistency** means the same entity information (name, address, logo, description) appears across multiple trusted sources like Wikipedia, social media, and official websites. 

**Authority** means Google prioritizes official websites, government databases, and established knowledge bases over less reliable sources. 

**Explicitness** means using structured data (Schema.org markup) and explicit identifiers like SameAs links and verified social profiles. 

The more platforms consistently describe the same entity with the same facts, and the more authoritative those platforms are, the faster and more confidently Google resolves the entity.


## Q3. When to Create Custom Post Types Instead of Pages

Create a **Custom Post Type** when you have multiple pieces of repeatable, structured content that need to be organized separately from blog posts and pages. Use **Pages** for static, one-off content like About, Contact, or Privacy Policy.

Examples of when to use a CPT:
1. Portfolios (each project has client, date, tools used)
2. Testimonials (each has author, rating, quote)
3. Events (each has date, location, time)
4. Team members (each has role, bio, photo)
5. Products (each has price, SKU, stock)

The rule of thumb: If you need more than 5-10 similar items that share the same custom fields or taxonomy, build a CPT. If it's a single static page, stick with Pages.


## Q4. Recommended Plugins for Speed Optimization and Why

**WP Rocket** – Best all-in-one caching plugin. Handles page caching, file minification (removing unnecessary characters from code to reduce file size), lazy loading (delaying images off-screen until user scrolls to them), and database optimization with minimal configuration.

**Perfmatters** – Lightweight plugin that disables unnecessary WordPress features (emoji, embeds, dashicons, XML-RPC) to reduce HTTP requests and load size.

**Imagify or ShortPixel** – Compresses images without quality loss. Large images are the #1 cause of slow load times.

**Smush** – Another solid image optimizer with bulk compression and lazy loading.

**Asset CleanUp** – Lets you unload CSS/JS files on pages where they aren't needed (prevents plugins from loading everywhere).

Why they work: Speed optimization is about reducing file sizes, fewer HTTP requests, and smarter caching. These plugins target the three biggest bottlenecks: images (largest files), CSS/JS (blocking rendering), and database queries (slow server response).

