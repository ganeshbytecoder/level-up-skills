# THE ARCHITECT'S CODEX
### *23 Patterns Every Master Builder Must Know — Discovered by One Engineer Who Almost Got Them All Wrong*

---

> *"A pattern is not a solution.*
> *It is the name for a problem that keeps appearing,*
> *and proof that someone, somewhere, solved it before you.*
> *Your job is not to be clever.*
> *Your job is to recognize which problem you are actually facing."*
>
> — **Corvin, First Architect of the Codex, 200 years before Priya was born**

---

## PROLOGUE — THE LIBRARY ON THE HILL

Two years after the night the River broke, Priya was promoted.

The title: **Lead Architect, Universal Trading Platform**. The mission: design and build the most complex system the city had ever attempted — a single platform through which every merchant in the known world could trade, regardless of which kingdom they came from, which currency they used, which guild they belonged to.

Seventeen kingdoms. Forty-two merchant types. A hundred years of incompatible legacy systems. Six months to build. One engineer to design it.

Priya had built things before. She had fixed failures before. But she had never *designed* something from a blank page.

On her first morning, she sat at her desk and stared at an empty parchment for two hours.

Then she remembered something Dev had said on the morning he left: *"When you don't know where to start — go to the Library on the Hill. Ask for the Codex. Corvin's Codex."*

She had assumed he was being poetic.

He was not.

---

The Library on the Hill was ancient — far older than the city's current form, older than Ara's redesign, older than the River itself. It occupied a small stone building at the top of the eastern ridge, staffed by one ancient librarian named **Fen**, who seemed to have been there as long as the books.

*"The Codex,"* Priya said.

Fen nodded as if he had been expecting her and walked her to a back room where a single heavy volume sat on a reading stand. The cover: worn leather, no title. Fen left without speaking.

Inside the front page, in cramped handwriting:

> *"Here are twenty-three problems that every builder eventually faces.*
> *I have named them. I have described them. I have given each its shape.*
> *A shape is powerful. Once you can name what you are fighting,*
> *you can find people who have already won.*
> *Use these patterns. Do not worship them.*
> *The pattern that fits no problem is a prison, not a solution."*
>
> *— Corvin*

Priya sat down, opened the Codex to page one, and began to build the Universal Trading Platform.

---

## PART I — THE CREATIONAL CHAPTERS
### *How Things Come Into Being*

---

### Pattern 1: The Singleton — *One Ledger. Only One.*

The first problem Priya faced was deceptively simple.

The Trading Platform needed an **Exchange Rate Registry** — a single source of truth for every currency conversion rate in the known world. Seventeen kingdoms, forty-two currencies, millions of transactions.

She started designing and immediately hit a problem: every service in the platform needed access to exchange rates. If each service created its own Registry, she would have seventeen copies of the Registry, each potentially out of sync.

She opened the Codex.

---

> **Corvin's Entry: The Royal Mint**
>
> *"In the kingdom of Valdrus, there was a time when any merchant could establish a Mint and print coins. The result was chaos: twelve different 'gold coins' of twelve different actual weights, each a 'gold coin' in name only.*
>
> *The King decreed: one Mint. One ledger. One authority.*
>
> *Not because gold was scarce. But because trust required a single source of truth.*
>
> *This is the Singleton. One instance of a thing. Not because you are incapable of making more — but because making more would be a disaster.*
>
> *Implementation: the object controls its own creation. It offers one way to get it, and that way always returns the same instance. It never exposes a constructor publicly. It stores its own single instance privately. All callers share it."*

---

> 💡 **Singleton Pattern**: Ensures a class has only one instance and provides a global access point to it.
>
> ```
> ExchangeRateRegistry {
>   private static instance = null
>
>   private constructor() { load rates from DB }
>
>   static getInstance() {
>     if (instance == null) instance = new Registry()
>     return instance
>   }
> }
> // Every caller: ExchangeRateRegistry.getInstance()
> // Same object. Always.
> ```
>
> **When to use**: Configuration objects, connection pools, logging services, caches — anything where one instance is both necessary and sufficient.
>
> **Thread safety**: In concurrent systems, double-checked locking or initialization-on-demand prevents multiple instances during startup.
>
> **Warning**: Singleton is the most abused pattern. Don't use it as a way to avoid passing dependencies — use it only when true single-instance is an architectural requirement.

One Registry. All services sharing it. Problem solved.

---

### Pattern 2: The Factory Method — *Hiring Without Knowing the Job Title*

The platform needed to create **payment processors** for every merchant type: grain traders paid in gold, spice merchants in silver tokens, foreign traders in letters of credit, digital guild members in River-credits.

Priya's first instinct: a massive `if` block. `if type == "grain" create GrainPaymentProcessor. if type == "spice" create SpicePaymentProcessor...`

She had written three lines of it before she recognized the smell — the smell of code that would require changes in seventeen places every time a new merchant type was added.

She opened the Codex.

---

> **Corvin's Entry: The Guild Recruitment Office**
>
> *"When the city needed a new worker, the Guild Master didn't know in advance what kind of worker would be needed. She only knew: 'I need a worker who can process payments.'*
>
> *She didn't create workers herself. She told the Recruitment Office: 'I need a payment worker.' The Recruitment Office knew, by its own rules, which type of worker to create. The Guild Master received a worker — she didn't know and didn't care which specific kind.*
>
> *This is the Factory Method. The creator defines what kind of object is needed (the interface), but defers the decision of which specific class to instantiate to a subclass or factory function.*
>
> *Add a new merchant type: add a new factory. Change nothing else."*

---

> 💡 **Factory Method Pattern**: Defines an interface for creating an object, but lets subclasses decide which class to instantiate. The creator works with the result through the interface, never the concrete class.
>
> ```
> interface PaymentProcessor {
>   process(amount, currency): Result
> }
>
> class GrainPaymentProcessor implements PaymentProcessor { ... }
> class SpicePaymentProcessor implements PaymentProcessor { ... }
>
> class PaymentProcessorFactory {
>   static create(merchantType): PaymentProcessor {
>     switch (merchantType) {
>       case "grain": return new GrainPaymentProcessor()
>       case "spice": return new SpicePaymentProcessor()
>       // Add new types here ONLY
>     }
>   }
> }
> ```
>
> **Key benefit**: The calling code is decoupled from concrete implementations. Adding a new merchant type requires adding one class + one factory entry — touching zero existing code.
>
> **Real-world**: Spring's `ApplicationContext.getBean()`, JDBC's `DriverManager.getConnection()`, Jackson's `ObjectMapper`.

---

### Pattern 3: The Abstract Factory — *Families That Belong Together*

The seventeen kingdoms each had their own way of doing things. The Northern Kingdom used parchment contracts signed in wax. The Eastern Kingdom used digital River-tokens. The Western Kingdom used verbal guild bonds.

Each kingdom needed a cohesive *family* of compatible objects: a contract generator, a payment verifier, and a dispute resolver — all working according to that kingdom's rules. The Northern contract generator would produce parchment contracts that only the Northern payment verifier could validate.

Mixing families — Northern contracts verified by Eastern tools — was catastrophic.

---

> **Corvin's Entry: The Kingdom Embassy**
>
> *"Every kingdom sent an Embassy to the city. The Embassy contained everything you needed to interact with that kingdom: their translator, their contract scribe, their payment agent. You went to the Northern Embassy and received Northern tools. You went to the Eastern Embassy and received Eastern tools. The tools always worked together because they came from the same Embassy.*
>
> *This is the Abstract Factory. An interface for creating families of related objects, without specifying their concrete classes. The factory guarantees compatibility within a family."*

---

> 💡 **Abstract Factory Pattern**: Provides an interface for creating families of related objects without specifying their concrete classes. Guarantees that objects from the same factory are compatible.
>
> ```
> interface TradingToolsFactory {
>   createContractGenerator(): ContractGenerator
>   createPaymentVerifier(): PaymentVerifier
>   createDisputeResolver(): DisputeResolver
> }
>
> class NorthernTradingFactory implements TradingToolsFactory {
>   createContractGenerator() { return new ParchmentContractGenerator() }
>   createPaymentVerifier()   { return new WaxSealVerifier() }
>   createDisputeResolver()   { return new NorthernGuildArbitrator() }
> }
>
> class EasternTradingFactory implements TradingToolsFactory {
>   createContractGenerator() { return new RiverTokenGenerator() }
>   createPaymentVerifier()   { return new DigitalSignatureVerifier() }
>   createDisputeResolver()   { return new EasternCourtArbitrator() }
> }
> ```
>
> **Key benefit**: Guarantees product family consistency. You can't accidentally mix a Northern contract with an Eastern verifier — the factory prevents it.
>
> **Difference from Factory Method**: Factory Method creates one product. Abstract Factory creates a family of related products.

---

### Pattern 4: The Builder — *Step by Step, or Not at All*

The platform's **Trade Agreement** was complex: it had parties, terms, payment schedules, penalties, optional clauses, dispute resolution methods, expiry dates, renewal conditions, and jurisdiction rules. Some were optional. Some were mutually exclusive. Some required others to be set first.

Creating a Trade Agreement with a single constructor required 23 parameters — a disaster Priya recognized immediately as a "telescoping constructor" nightmare.

---

> **Corvin's Entry: The Custom Shipyard**
>
> *"You didn't order a ship by shouting specifications at the dockmaster all at once. You sat with the ship's builder. He asked: How many masts? What cargo capacity? Which kind of hull for which waters? He built it in stages. Each step was clear. Each step was optional or required as the design demanded. When you said 'done,' he handed you a complete ship.*
>
> *This is the Builder. Separate the construction of a complex object from its representation, so the same construction process can create different representations.*
>
> *The builder accumulates configuration. The final 'build()' call produces the complete object. No invalid intermediate states. No 23-argument constructor."*

---

> 💡 **Builder Pattern**: Separates complex object construction from its representation. Enables step-by-step creation with optional parameters.
>
> ```
> TradeAgreement agreement = new TradeAgreementBuilder()
>   .withParties(merchant1, merchant2)
>   .withPaymentSchedule(monthly)
>   .withPenaltyClause(10_percent)
>   .withJurisdiction(NorthernKingdom)
>   .withExpiryDate(oneYear)
>   .build();  // Validates required fields, returns immutable object
> ```
>
> **Key benefits**:
> - No telescoping constructors (23-parameter constructors are builder opportunities)
> - Optional parameters without overloaded constructors
> - `build()` can validate that required fields are set before creating the object
> - Same builder process can produce different configurations
>
> **Real-world**: `StringBuilder`, `HttpRequest.newBuilder()`, Lombok's `@Builder`, SQL query builders.

---

### Pattern 5: The Prototype — *Copy, Don't Rebuild*

The platform had **40 Standard Agreement Templates** — pre-configured Trade Agreements for common scenarios. A grain-for-silver trade between Northern and Eastern kingdoms had a standard template that was used hundreds of times per day.

Each time, the code fetched the template from the database, deserialized it, and configured it. The database queries alone were adding 200ms to every trade initiation.

---

> **Corvin's Entry: The Map Copying Hall**
>
> *"The Kingdom's cartographers produced one definitive map of each territory each year. Rather than re-draw the map from scratch each time a general needed one, they kept the original in the Map Hall, and copied it. Copy the map, mark your route on the copy, leave the original pristine.*
>
> *This is the Prototype. Instead of creating new instances of expensive-to-create objects, clone an existing instance. The original is the prototype. All new instances start as copies of it.*
>
> *Used when object creation is expensive — involves database calls, file parsing, complex initialization — and you often need many similar objects."*

---

> 💡 **Prototype Pattern**: Creates new objects by copying an existing object (the prototype). Avoids the cost of creating from scratch when creation is expensive.
>
> ```
> interface Cloneable {
>   clone(): TradeAgreement
> }
>
> class TradeAgreementTemplate implements Cloneable {
>   clone() {
>     return deepCopy(this) // Copy all fields, including nested objects
>   }
> }
>
> // At startup: load all 40 templates once
> Map<String, TradeAgreementTemplate> templates = loadFromDB()
>
> // At runtime: clone in microseconds, no DB call
> TradeAgreement newAgreement = templates.get("GRAIN_SILVER_N_E").clone()
> newAgreement.setParties(merchant1, merchant2)
> ```
>
> **Key benefit**: Avoids expensive re-initialization. Instead of 200ms per trade initiation (DB + deserialization), cloning takes microseconds.
>
> **Warning**: Deep copy vs. shallow copy — be explicit. A shallow clone shares references to nested objects; changes to the clone's nested objects affect the prototype.

---

## PART II — THE STRUCTURAL CHAPTERS
### *How Things Fit Together*

---

### Pattern 6: The Adapter — *Making the Incompatible Speak*

The historic Kingdom of Aldenmere had been trading for 600 years. Their legacy system used a payment protocol from the Second Era — an entirely different format, different field names, different data types. They had no interest in updating it. Their system worked, had worked for six centuries, and would continue to work.

But the Trading Platform needed to communicate with it.

---

> **Corvin's Entry: The Translation Bureau**
>
> *"The Northern merchants spoke only their own tongue. The Eastern traders spoke only theirs. Rather than require either to learn the other's language, the city established a Translation Bureau. Every message entered in Northern tongue; it emerged in Eastern tongue. The Northerners spoke to the Bureau as if speaking to Easterners. The Bureau did the work of translation in between.*
>
> *This is the Adapter. A class that makes two incompatible interfaces work together. The adapter wraps the old interface and presents the new one. Neither side needs to change."*

---

> 💡 **Adapter Pattern**: Converts the interface of a class into another interface that clients expect. Makes incompatible classes work together without modifying their source code.
>
> ```
> // The Trading Platform expects:
> interface ModernPaymentGateway {
>   processPayment(transactionId: String, amount: Money): Receipt
> }
>
> // Aldenmere's ancient system has:
> class AncientAldenmereSystem {
>   execute_tran(tran_no: Int, gold_units: Int, silver_tenths: Int): AncientReceipt
> }
>
> // The Adapter:
> class AldenmereAdapter implements ModernPaymentGateway {
>   ancient = new AncientAldenmereSystem()
>
>   processPayment(transactionId, amount) {
>     int tranNo = Integer.parseInt(transactionId)
>     int gold = amount.wholeUnits()
>     int silver = amount.tenthsOfSilver()
>     AncientReceipt r = ancient.execute_tran(tranNo, gold, silver)
>     return modernize(r)
>   }
> }
> ```
>
> **Key benefit**: Integrate legacy systems or third-party libraries without touching their code. The adapter is the only place that knows about the incompatibility.
>
> **Real-world**: Java's `InputStreamReader` (adapts byte stream to character stream), Spring's `HandlerAdapter`.

---

### Pattern 7: The Bridge — *Separate What Changes From What Doesn't*

The Trading Platform displayed trade data in three formats: **report scrolls** (human-readable), **River messages** (machine-readable), **visual maps** (graphical). And it had two categories of data: **real-time trades** and **historical archives**.

Priya's first design: `RealtimeReportScroll`, `RealtimeRiverMessage`, `RealtimeVisualMap`, `HistoricalReportScroll`, `HistoricalRiverMessage`, `HistoricalVisualMap`. Six classes. Next quarter: two new data sources + one new format = 6 more classes. This was going to explode.

---

> **Corvin's Entry: The Dual-Rail System**
>
> *"The kingdom had two kinds of cargo (heavy goods, light goods) and three kinds of carriages (open car, enclosed car, refrigerated car). Rather than build six carriage designs for each combination, the engineers built two kinds of rail — one for each cargo type — and three universal carriage designs that could run on either rail.*
>
> *Abstraction (cargo type) and implementation (carriage design) were separated. New cargo type: add a new rail. New carriage design: build one design, runs on all rails.*
>
> *This is the Bridge. Decouple an abstraction from its implementation so the two can vary independently."*

---

> 💡 **Bridge Pattern**: Decouples abstraction from implementation. Both can vary independently without a combinatorial explosion of subclasses.
>
> ```
> // Implementation hierarchy (renderers)
> interface TradeRenderer {
>   renderTrade(data: TradeData): Output
> }
> class ScrollRenderer implements TradeRenderer { ... }
> class RiverMessageRenderer implements TradeRenderer { ... }
> class VisualMapRenderer implements TradeRenderer { ... }
>
> // Abstraction hierarchy (data sources)
> abstract class TradeDataSource {
>   renderer: TradeRenderer  // THE BRIDGE
>   constructor(renderer) { this.renderer = renderer }
>   abstract getData(): TradeData
>   display() { renderer.renderTrade(getData()) }
> }
> class RealtimeTradeSource extends TradeDataSource { ... }
> class HistoricalArchiveSource extends TradeDataSource { ... }
>
> // Usage: any combination, no new classes
> new RealtimeTradeSource(new ScrollRenderer()).display()
> new HistoricalArchiveSource(new VisualMapRenderer()).display()
> ```
>
> **Key benefit**: Adding a new renderer requires one new class. Adding a new data source requires one new class. Never 2×3=6 classes for combinations.

---

### Pattern 8: The Composite — *One and Many, Treated the Same*

The Trading Platform organized merchants into **guilds**, and guilds into **guild federations**, and federations into **kingdom alliances**. Applying a trade regulation meant applying it to one merchant, or a guild of merchants, or a federation — the logic should be identical from the caller's perspective.

Priya's early code had three separate paths: `applyToMerchant()`, `applyToGuild()`, `applyToFederation()`. Every new regulation had to implement all three.

---

> **Corvin's Entry: The Military Command Structure**
>
> *"The General issued orders. He didn't need to know if he was addressing a single soldier, a platoon, or an entire brigade. He issued the order: 'March north.' The soldier marched. The platoon marched. The brigade marched. The order was identical.*
>
> *This is the Composite. Treat individual objects and compositions of objects uniformly. A leaf and a tree share the same interface. Whether the caller is addressing one item or a million, the call looks the same."*

---

> 💡 **Composite Pattern**: Composes objects into tree structures. Clients treat individual objects and composites of objects uniformly.
>
> ```
> interface TradingEntity {
>   applyRegulation(regulation: Regulation): void
>   getTotalTradeVolume(): Money
> }
>
> class Merchant implements TradingEntity {
>   applyRegulation(reg) { this.regulations.add(reg) }
>   getTotalTradeVolume() { return this.trades.sum() }
> }
>
> class Guild implements TradingEntity {
>   members: TradingEntity[]  // Can contain Merchants OR other Guilds
>   applyRegulation(reg) { members.forEach(m => m.applyRegulation(reg)) }
>   getTotalTradeVolume() { return members.map(m => m.getTotalTradeVolume()).sum() }
> }
>
> // Apply to a federation of guilds of merchants — one call
> kingdom.applyRegulation(newTariff)
> ```
>
> **Key benefit**: Uniform treatment of single objects and complex hierarchies. Recursive structures (trees) become natural.
>
> **Real-world**: File systems (files and folders), UI components (widgets and containers), XML/JSON parsing.

---

### Pattern 9: The Decorator — *Adding Capabilities Without Breaking the Original*

A basic `TradeAgreement` had core functionality. But some agreements needed:
- **Encryption** (sensitive trade data)
- **Audit logging** (for compliance)
- **Compression** (large contracts)
- **Digital signing** (legal requirement)

Some needed all four. Some needed two. In any combination, in any order.

Subclassing: `EncryptedTradeAgreement`, `AuditedTradeAgreement`, `EncryptedAuditedTradeAgreement`, `CompressedSignedAuditedTradeAgreement`... the combinations were infinite.

---

> **Corvin's Entry: The Tailor's Workshop**
>
> *"A tailor had one standard merchant's coat. Some customers wanted it lined (warmer). Some wanted it waterproofed. Some wanted both. Some wanted embroidered shoulders too. He didn't make a different coat for every combination. He made the coat, and added linings, waterproofing, and embroidery as separate layers — each layer wrapping the previous, each adding its own capability.*
>
> *This is the Decorator. Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality."*

---

> 💡 **Decorator Pattern**: Wraps an object in another object with the same interface, adding behavior before or after delegating to the wrapped object.
>
> ```
> interface Agreement {
>   execute(): Result
> }
>
> class BaseAgreement implements Agreement {
>   execute() { return processTerms() }
> }
>
> class EncryptionDecorator implements Agreement {
>   wrapped: Agreement
>   constructor(a: Agreement) { this.wrapped = a }
>   execute() {
>     encrypt(this)
>     Result r = wrapped.execute()
>     decrypt(r)
>     return r
>   }
> }
>
> class AuditDecorator implements Agreement {
>   wrapped: Agreement
>   execute() { log("start"); Result r = wrapped.execute(); log("end"); return r }
> }
>
> // Compose any combination at runtime
> Agreement a = new AuditDecorator(
>                 new EncryptionDecorator(
>                   new CompressionDecorator(
>                     new BaseAgreement())))
> ```
>
> **Key benefit**: Any combination of behaviors without subclass explosion. Behaviors can be added and removed at runtime.
>
> **Real-world**: Java I/O streams, HTTP middleware, Spring's AOP, Express.js middleware.

---

### Pattern 10: The Facade — *One Door to a Complicated House*

A new guild wanted to join the Trading Platform. The onboarding process involved: identity verification (5 steps), contract generation (3 steps), payment gateway configuration (7 steps), River subscription setup (4 steps), regulatory compliance check (6 steps), and welcome notification (2 steps).

Twenty-seven steps. The joining guild's engineers called each step individually, in the wrong order, with missing parameters.

---

> **Corvin's Entry: The City Welcome Hall**
>
> *"When a new merchant arrived in the city, they didn't navigate to the Tax Bureau, then the Guild Registry, then the Housing Office, then the Market Assignment Office separately. They went to the Welcome Hall. 'I am a new merchant,' they said. The Welcome Hall did everything else.*
>
> *This is the Facade. A simplified interface to a complex subsystem. The subsystem is still there, still complex. The Facade hides that complexity behind one simple surface."*

---

> 💡 **Facade Pattern**: Provides a simplified interface to a complex subsystem. The subsystem isn't changed — the facade makes it easier to use.
>
> ```
> class MerchantOnboardingFacade {
>   identityService: IdentityService
>   contractService: ContractService
>   paymentService: PaymentGatewayService
>   riverService: RiverSubscriptionService
>   complianceService: ComplianceService
>   notificationService: NotificationService
>
>   onboardMerchant(merchant: Merchant): OnboardingResult {
>     identity = identityService.verifyInFiveSteps(merchant)
>     contract = contractService.generateInThreeSteps(identity)
>     payment  = paymentService.configureInSevenSteps(merchant)
>     // ... all 27 steps, in the right order, with error handling
>     notificationService.sendWelcome(merchant)
>     return OnboardingResult.success()
>   }
> }
>
> // Guild engineer's experience:
> facade.onboardMerchant(myGuild)  // Done.
> ```
>
> **Key benefit**: Simplifies complex APIs for common use cases. Subsystems can still be accessed directly for advanced use.
>
> **Real-world**: SLF4J (facade over logging frameworks), JDBC (facade over database drivers), REST APIs (facade over business logic).

---

### Pattern 11: The Flyweight — *Share the Common, Isolate the Unique*

The Trading Platform displayed 50 million **trade history records** in the merchant dashboard. Each record contained: a trade type object, a currency object, a kingdom object, and a merchant category object — all of which were repeated millions of times throughout the dataset.

Loading 50 million distinct objects would consume 40GB of memory. The server had 8GB.

---

> **Corvin's Entry: The Coin Mint**
>
> *"The kingdom minted a billion coins. But the design of a 'gold coin' was the same for every coin ever minted — the same image, the same inscription. Rather than store the design on each physical coin, the design lived once in the Mint's master die, and was stamped onto each coin. The coin itself stored only what was unique: its serial number and its owner.*
>
> *Shared state (the design) stored once. Unique state (serial number, owner) stored per-instance.*
>
> *This is the Flyweight. Share common state among many fine-grained objects. Reduce memory by extracting repeated data into shared objects."*

---

> 💡 **Flyweight Pattern**: Uses sharing to efficiently support large numbers of fine-grained objects. Splits object state into intrinsic (shared, immutable) and extrinsic (unique per instance).
>
> ```
> // Intrinsic state (shared) — created once, reused by millions
> class TradeTypeData {  // "GRAIN", "SPICE", etc.
>   finalDescription, taxRate, regulatoryCode  // Same for all grain trades
> }
>
> // Flyweight factory — returns shared instances
> class TradeTypeFactory {
>   private static cache: Map<String, TradeTypeData> = {}
>   static get(typeName): TradeTypeData {
>     if (!cache[typeName]) cache[typeName] = new TradeTypeData(typeName)
>     return cache[typeName]  // Same object returned every time
>   }
> }
>
> // Extrinsic state (unique) — stored per trade record
> class TradeRecord {
>   tradeType: TradeTypeData  // Shared flyweight reference
>   uniqueAmount: Money       // Unique to this record
>   uniqueDate: DateTime      // Unique to this record
> }
> // 50M records share ~20 TradeTypeData objects instead of 50M
> ```
>
> **Key benefit**: Massive memory reduction when many objects share common state.
>
> **Real-world**: Java's `String` interning, integer caching (-128 to 127), character glyphs in text rendering.

---

### Pattern 12: The Proxy — *Control What You Don't Want to Expose Directly*

The Royal Treasury's financial data was extraordinarily sensitive. Only certain merchants, under certain conditions, at certain times, could access certain records. Direct access to the Treasury object meant direct access to all data.

Additionally, loading the Treasury object itself required a 3-second initialization (connecting to three separate secure vaults). Most services needed it rarely — loading it eagerly for every service instance was wasteful.

---

> **Corvin's Entry: The Royal Secretary**
>
> *"Nobody walked directly into the King's chamber. They went through the Royal Secretary. The Secretary verified your identity, checked your appointment, decided you could enter or not, and logged your visit. The King himself never had to deal with uninvited visitors.*
>
> *The Secretary was a Proxy — same interface as the King (could receive visitors), but with full control over access.*
>
> *The Secretary also prevented the King from being disturbed until someone actually needed him — the King wasn't summoned until the Secretary had verified the appointment was real."*

---

> 💡 **Proxy Pattern**: Provides a surrogate or placeholder for another object to control access to it.
>
> **Three flavors**:
>
> **Virtual Proxy** (lazy initialization):
> ```
> class TreasuryProxy implements Treasury {
>   private realTreasury: RealTreasury = null  // Not loaded yet
>   getRecord(merchantId) {
>     if (realTreasury == null) realTreasury = new RealTreasury()  // Load on first use
>     return realTreasury.getRecord(merchantId)
>   }
> }
> ```
>
> **Protection Proxy** (access control):
> ```
> class SecureTreasuryProxy implements Treasury {
>   getRecord(merchantId, caller) {
>     if (!authService.isAuthorized(caller, "TREASURY_READ"))
>       throw UnauthorizedException()
>     return realTreasury.getRecord(merchantId)
>   }
> }
> ```
>
> **Key benefit**: Lazy loading prevents expensive initialization until needed. Access control is centralized in the proxy, not scattered across all callers.
>
> **Real-world**: Spring's `@Lazy`, Hibernate's lazy-loaded relationships, API rate-limiting proxies, NGINX as reverse proxy.

---

## PART III — THE BEHAVIORAL CHAPTERS
### *How Things Talk to Each Other*

---

### Pattern 13: The Chain of Responsibility — *Pass Until Someone Handles It*

Every trade on the platform went through a **validation pipeline**: first, check basic format. Then, check regulatory compliance. Then, check fraud signals. Then, check kingdom-specific rules. Then, check merchant credit standing.

Each validator was entirely different code. Some trades needed only the first two checks (simple domestic trades). Some needed all five. The decision of which validator handled which trade should not be hardcoded in the calling code.

---

> **Corvin's Entry: The Tax Appeal Court**
>
> *"When a merchant appealed a tax decision, the appeal went first to the Local Commissioner. If the Commissioner could resolve it — they did. If not, it passed to the Regional Governor. If the Governor couldn't handle it, it went to the Royal Court.*
>
> *Each handler in the chain either handled the request or passed it forward. The merchant didn't need to know where it would ultimately be resolved. They simply submitted the appeal.*
>
> *This is the Chain of Responsibility. Pass a request along a chain of handlers. Each handler either processes it or passes it to the next."*

---

> 💡 **Chain of Responsibility Pattern**: A chain of handlers, each with a chance to handle a request or pass it along. Decouples the sender from the handler.
>
> ```
> abstract class TradeValidator {
>   next: TradeValidator = null
>   setNext(v: TradeValidator) { this.next = v; return v }
>
>   validate(trade: Trade): ValidationResult {
>     result = this.check(trade)
>     if (result.failed || next == null) return result
>     return next.validate(trade)
>   }
>
>   abstract check(trade): ValidationResult
> }
>
> class FormatValidator extends TradeValidator { ... }
> class RegulatoryValidator extends TradeValidator { ... }
> class FraudValidator extends TradeValidator { ... }
>
> // Build the chain
> FormatValidator format = new FormatValidator()
> format.setNext(new RegulatoryValidator())
>       .setNext(new FraudValidator())
>       .setNext(new KingdomRulesValidator())
>
> format.validate(trade)  // Flows through chain
> ```
>
> **Key benefit**: Add, remove, or reorder validators without touching the caller or other validators.
>
> **Real-world**: HTTP middleware chains (Express, Spring filters), logging handlers, exception handlers.

---

### Pattern 14: The Command — *Turn an Action Into an Object*

The Royal Trading Council needed to:
- Execute trades
- Queue trades for later
- Undo trades that were submitted incorrectly
- Log all actions for audit
- Replay sequences of trades for testing

All of these required the trade *action* to be an object — something that could be stored, passed around, executed later, or reversed.

---

> **Corvin's Entry: The Royal Orders System**
>
> *"The King didn't carry out his decrees himself. He wrote them on official scrolls. The scrolls were the Orders — objects containing: what to do, who to do it to, and what to do to undo it. Orders could be queued, delayed, delegated, or reversed. The Royal Scribe kept every Order ever written.*
>
> *This is the Command. Encapsulate a request as an object, thereby allowing you to parameterize, queue, log, and undo operations."*

---

> 💡 **Command Pattern**: Encapsulates a request as an object. Enables queuing, logging, undo/redo, and macro operations.
>
> ```
> interface TradeCommand {
>   execute(): void
>   undo(): void
> }
>
> class ExecuteTradeCommand implements TradeCommand {
>   trade: Trade
>   constructor(trade) { this.trade = trade }
>   execute() { tradingEngine.process(trade) }
>   undo()    { tradingEngine.reverse(trade) }
> }
>
> class CommandHistory {
>   stack: TradeCommand[] = []
>   execute(cmd: TradeCommand) { cmd.execute(); stack.push(cmd) }
>   undoLast() { if (stack.length > 0) stack.pop().undo() }
> }
>
> // Queue for later execution
> commandQueue.add(new ExecuteTradeCommand(grainsForSilverTrade))
>
> // Undo last 3 actions
> history.undoLast(); history.undoLast(); history.undoLast()
> ```
>
> **Key benefit**: Decouples the object that issues a request from the objects that perform it. Enables undo/redo naturally.
>
> **Real-world**: Database transactions (all db operations are commands), Git commits, UI undo/redo, task queues (Celery, Sidekiq).

---

### Pattern 15: The Iterator — *Traverse Without Knowing the Structure*

The platform stored merchant lists in different data structures for different access patterns: a hash table (fast lookup by ID), a sorted tree (fast range queries), and a linked list (fast sequential insertion).

Code that needed to iterate over merchants shouldn't need to know which structure was used — that was an implementation detail, not the caller's concern.

---

> **Corvin's Entry: The Library Card Catalog**
>
> *"The library stored books in many ways — by author on wooden shelves, by publication date in rolling cabinets, by topic in hanging folders. The card catalog provided one consistent way to walk through any collection: 'has next card?', 'get next card.' The reader didn't need to know which physical storage was behind the catalog.*
>
> *This is the Iterator. Provide a way to access elements of a collection sequentially without exposing the underlying representation."*

---

> 💡 **Iterator Pattern**: Provides a standard way to traverse a collection without exposing its implementation.
>
> ```
> interface Iterator<T> {
>   hasNext(): boolean
>   next(): T
> }
>
> class MerchantTreeIterator implements Iterator<Merchant> {
>   // Internally: in-order traversal of balanced BST
>   hasNext() { return inorderStack.notEmpty() }
>   next() { return inorderStack.pop() }
> }
>
> class MerchantHashIterator implements Iterator<Merchant> {
>   // Internally: iterate over hash buckets
>   hasNext() { return currentBucket < buckets.length }
>   next() { return nextFromCurrentBucket() }
> }
>
> // Caller is identical regardless of implementation:
> Iterator<Merchant> iter = getMerchantIterator()
> while (iter.hasNext()) {
>   process(iter.next())
> }
> ```
>
> **Key benefit**: Calling code is independent of collection implementation. Swap the structure without changing traversal code.
>
> **Real-world**: Java's `Iterator`, Python's `__iter__`/`__next__`, database cursors, stream pipelines.

---

### Pattern 16: The Mediator — *One Central Voice*

The Trading Platform had seven active services: the Price Service, the Credit Service, the Fraud Service, the Regulatory Service, the Notification Service, the Audit Service, and the Settlement Service.

In the original design, they called each other directly: the Price Service notified the Notification Service. The Credit Service notified the Fraud Service. The Fraud Service called Settlement. Seven services, each calling multiple others — forty-two possible connections, each needing to be maintained.

When the Fraud Service's API changed, six services needed updating.

---

> **Corvin's Entry: The Stock Exchange Floor**
>
> *"On the trading floor of the Great Market, a hundred merchants stood in a circle. Early in the city's history, each merchant shouted directly to every other merchant they wanted to trade with — chaos. Someone invented the Auctioneer. Every bid and offer went to the Auctioneer. The Auctioneer decided who heard what. No merchant knew or cared who the counterparty was.*
>
> *This is the Mediator. Define an object that encapsulates how a set of objects interact. Reduce direct connections between objects by routing communication through a central coordinator."*

---

> 💡 **Mediator Pattern**: An object that coordinates interactions between multiple objects, preventing them from referring to each other directly.
>
> ```
> class TradingMediator {
>   // All services registered here, not with each other
>   priceService, creditService, fraudService, settlementService...
>
>   notify(sender: Service, event: String, data: Any) {
>     switch (event) {
>       case "TRADE_SUBMITTED":
>         creditService.check(data)
>         fraudService.analyze(data)
>       case "FRAUD_DETECTED":
>         settlementService.block(data)
>         notificationService.alert(data)
>       case "CREDIT_APPROVED":
>         settlementService.proceed(data)
>     }
>   }
> }
>
> // Services communicate only with the Mediator:
> mediator.notify(this, "TRADE_SUBMITTED", tradeData)
> ```
>
> **Key benefit**: N×(N-1)/2 connections become N connections. Change one service without touching any other.
>
> **Real-world**: Chat room (mediator between users), air traffic control (mediator between planes), event bus (mediator between publishers and subscribers).

---

### Pattern 17: The Memento — *Remember How Things Were*

Merchants could create draft Trade Agreements in stages over multiple sessions. They needed to:
- Save their work-in-progress
- Return later and continue
- Roll back to a previous version if they changed their mind
- Compare current state to yesterday's draft

Priya needed to save and restore the Agreement's state — without exposing its internals to the storage mechanism.

---

> **Corvin's Entry: The Royal Document Archive**
>
> *"When the King signed a treaty, the Royal Scribe made a sealed copy — a Memento — and stored it in the Archive. If the treaty was later disputed or a previous version needed to be recalled, the Scribe retrieved the sealed copy. The seal meant it hadn't been tampered with. Its contents were opaque to the Archivist — he stored and retrieved it, but never read it.*
>
> *This is the Memento. Capture an object's internal state so it can be restored later, without violating encapsulation."*

---

> 💡 **Memento Pattern**: Captures and externalizes an object's internal state so it can be restored later, without exposing implementation details.
>
> ```
> class TradeAgreement {
>   private terms, parties, conditions  // internal state
>
>   save(): Memento {
>     return new Memento(deepCopy({terms, parties, conditions}))
>   }
>   restore(m: Memento) {
>     {terms, parties, conditions} = m.getState()
>   }
> }
>
> class Memento {
>   private state  // Opaque to everyone except TradeAgreement
>   constructor(state) { this.state = state }
>   getState() { return this.state }
> }
>
> class DraftHistory {  // The Caretaker — stores but never reads Mementos
>   history: Memento[] = []
>   save(agreement) { history.push(agreement.save()) }
>   undo(agreement) { if (history.length) agreement.restore(history.pop()) }
> }
> ```
>
> **Key benefit**: Undo/redo functionality with no exposure of internal state. The Caretaker stores Mementos but can't read or modify them.
>
> **Real-world**: Browser history, git stash, database savepoints, game save states.

---

### Pattern 18: The Observer — *Tell Everyone Who Needs to Know*

When a trade's status changed — from SUBMITTED to APPROVED, from APPROVED to SETTLED — a dozen different subsystems needed to react: send a notification, update the audit log, recalculate credit limits, refresh the dashboard, trigger River events, update analytics.

Priya's first implementation: the Settlement Service directly called all twelve. Every new subscriber required modifying the Settlement Service. When the Analytics service was added, the Settlement Service's code was changed — which had nothing to do with settlement.

---

> **Corvin's Entry: The City Bell System**
>
> *"When the City Clock struck the hour, the Hour Bell rang. It didn't know who was listening. The baker started the oven. The guards changed shifts. The market opened. The children went to school. Nobody had to tell the Clock about them. They had registered themselves as listeners to the Bell. New listener? Register with the Bell. No change to the Clock.*
>
> *This is the Observer. Define a one-to-many dependency so that when one object changes state, all its dependents are notified and updated automatically."*

---

> 💡 **Observer Pattern**: Defines a one-to-many dependency. When the subject changes state, all registered observers are notified automatically.
>
> ```
> interface TradeObserver {
>   onTradeStatusChanged(trade: Trade, newStatus: Status): void
> }
>
> class TradeSettlement {
>   observers: TradeObserver[] = []
>   addObserver(o: TradeObserver) { observers.push(o) }
>   removeObserver(o: TradeObserver) { observers.remove(o) }
>
>   settleT(trade) {
>     processTrade(trade)
>     trade.status = SETTLED
>     observers.forEach(o => o.onTradeStatusChanged(trade, SETTLED))
>     // Settlement doesn't know or care who is notified
>   }
> }
>
> class NotificationService implements TradeObserver {
>   onTradeStatusChanged(trade, status) { sendSMS(trade.merchant) }
> }
> class AnalyticsService implements TradeObserver {
>   onTradeStatusChanged(trade, status) { updateDashboard(trade) }
> }
>
> // Add new observer without touching Settlement:
> settlement.addObserver(new NewComplianceService())
> ```
>
> **Key benefit**: Loose coupling between subject and observers. Add observers without modifying subject.
>
> **Real-world**: Java's `EventListener`, JavaScript's `addEventListener`, React's state/effect system, Kafka (observers = consumers), RxJava.

---

### Pattern 19: The State — *Be What the Moment Requires*

A Trade Agreement passed through states: `DRAFT → SUBMITTED → UNDER_REVIEW → APPROVED → ACTIVE → EXPIRED`. Each state allowed different operations: a DRAFT could be edited; a SUBMITTED agreement could not; an APPROVED agreement could be activated; an EXPIRED one could only be archived.

Priya's original implementation: every method checked `if (state == DRAFT) ... else if (state == SUBMITTED) ... else if (state == APPROVED)...` — a matrix of conditions that grew exponentially with each new state.

---

> **Corvin's Entry: The Traffic Gate**
>
> *"The city gate behaved differently depending on its state. When OPEN: allow all passage. When LOCKED: allow no passage. When INSPECTION: allow merchants through one at a time, checking papers. The gate was one object — but its behavior changed entirely based on which state it was in. The gate didn't check its state with an if-block; it simply was in a state, and that state defined what it did.*
>
> *This is the State. Allow an object to alter its behavior when its internal state changes. The object will appear to change its class."*

---

> 💡 **State Pattern**: Allows an object to change its behavior when its internal state changes. Each state is an object that handles requests according to that state's rules.
>
> ```
> interface AgreementState {
>   edit(agreement): void
>   submit(agreement): void
>   approve(agreement): void
>   activate(agreement): void
> }
>
> class DraftState implements AgreementState {
>   edit(a)     { a.updateTerms() }           // Allowed
>   submit(a)   { a.setState(new SubmittedState()) }
>   approve(a)  { throw "Cannot approve a draft" }   // Not allowed
>   activate(a) { throw "Cannot activate a draft" }
> }
>
> class SubmittedState implements AgreementState {
>   edit(a)     { throw "Cannot edit submitted agreement" }
>   submit(a)   { throw "Already submitted" }
>   approve(a)  { a.setState(new ApprovedState()) }
>   activate(a) { throw "Must be approved first" }
> }
>
> class TradeAgreement {
>   state: AgreementState = new DraftState()
>   setState(s) { this.state = s }
>   edit()    { state.edit(this) }     // Delegates to current state
>   approve() { state.approve(this) }
> }
> ```
>
> **Key benefit**: Eliminates complex conditional logic. Adding a new state requires one new class, not dozens of new conditions scattered throughout.
>
> **Real-world**: Vending machine, traffic lights, order status management, connection lifecycle.

---

### Pattern 20: The Strategy — *Swappable Algorithms*

The Trading Platform calculated tariffs differently for seventeen kingdoms. Some used flat rates. Some used percentage-based calculation. Some used tiered brackets. Some used seasonal multipliers.

The calculation logic belonged outside the core Trade class — it was a policy decision, not a business fact.

---

> **Corvin's Entry: The Tax Calculation Office**
>
> *"The Tax Office had different calculation methods for different merchant categories. Rather than rewrite the Tax Office when the Northern Kingdom introduced a new tiered system, the Office kept each calculation method in a separate scroll: 'FlatRateMethod,' 'PercentageMethod,' 'TieredMethod.' When the Northern Kingdom changed its rules, only its scroll changed. The Tax Office itself was untouched.*
>
> *This is the Strategy. Define a family of algorithms, encapsulate each one, and make them interchangeable. The algorithm varies independently from the client that uses it."*

---

> 💡 **Strategy Pattern**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable. The client chooses which algorithm to use — without knowing its implementation.
>
> ```
> interface TariffStrategy {
>   calculate(tradeAmount: Money, kingdom: Kingdom): Money
> }
>
> class FlatRateTariff implements TariffStrategy {
>   calculate(amount, kingdom) { return Money.of(50) }
> }
> class PercentageTariff implements TariffStrategy {
>   calculate(amount, kingdom) { return amount.multiply(0.05) }
> }
> class TieredTariff implements TariffStrategy {
>   calculate(amount, kingdom) { return applyBrackets(amount) }
> }
>
> class TradeTransaction {
>   strategy: TariffStrategy
>   constructor(kingdom) {
>     this.strategy = TariffStrategyFactory.getFor(kingdom)
>   }
>   calculateTariff(amount) { return strategy.calculate(amount, kingdom) }
> }
>
> // Change Northern Kingdom's method: update TariffStrategyFactory only
> ```
>
> **Key benefit**: Algorithms are interchangeable at runtime. New algorithm = new class, not modified existing code.
>
> **Real-world**: Sorting algorithms, payment processor selection, compression strategy, route optimization.

---

### Pattern 21: The Template Method — *The Recipe With Customizable Steps*

All trade report generation followed the same overall flow: **fetch data → validate → format → apply header/footer → output**. But for different report types (monthly summary, audit trail, regulatory report), the "format" step differed completely.

---

> **Corvin's Entry: The Bread Guild Recipe**
>
> *"The Master Baker had one recipe: Mix. Knead. Prove. Shape. Bake. Every baker followed these steps in order — no exceptions. But the 'Shape' step was different for loaves, rolls, or braids. The recipe didn't define how to shape; it defined that shaping must happen at step 4. Each baker implemented their own shaping.*
>
> *This is the Template Method. Define the skeleton of an algorithm in a base class, deferring specific steps to subclasses. Subclasses can override specific steps without changing the overall algorithm's structure."*

---

> 💡 **Template Method Pattern**: Defines the skeleton of an algorithm in a base class. Specific steps are implemented (or overridden) by subclasses. The overall structure is fixed; the details vary.
>
> ```
> abstract class TradeReportGenerator {
>   // The template method — final, cannot be overridden
>   final generateReport(tradeId): Report {
>     data    = fetchData(tradeId)   // Same for all
>     valid   = validate(data)       // Same for all
>     body    = format(data)         // OVERRIDDEN by subclasses
>     report  = applyHeaderFooter(body)  // Same for all
>     return output(report)          // Same for all
>   }
>
>   abstract format(data: TradeData): FormattedContent
> }
>
> class MonthlyReportGenerator extends TradeReportGenerator {
>   format(data) { return groupByMonth(data).toTable() }
> }
> class AuditReportGenerator extends TradeReportGenerator {
>   format(data) { return detailEveryAction(data).withTimestamps() }
> }
> ```
>
> **Key benefit**: Common logic written once in the base class. Subclasses only implement what varies. The overall flow can never accidentally be changed.
>
> **Real-world**: JUnit's `setUp`/`tearDown`, Spring's `JdbcTemplate`, HTTP request handlers.

---

### Pattern 22: The Visitor — *New Operations Without Touching the Classes*

The Trading Platform had a hierarchy of trade document types: `SimpleContract`, `ComplexDerivativeContract`, `FuturesContract`, `SpotContract`. Tax compliance required calculating taxes differently for each type. So did audit reporting. So did risk assessment.

Adding each new operation (tax calc, audit report, risk) required modifying every contract class. Twenty-three contract types × three operations = sixty-nine ad-hoc additions scattered through the codebase.

---

> **Corvin's Entry: The Royal Tax Inspector**
>
> *"The Tax Department sent one Inspector to visit every business in the city. The Inspector knew how to assess taxes for a bakery, a smithy, a trading house, a guild hall — each differently. The businesses themselves didn't calculate their own taxes. They accepted the Inspector's visit. The Inspector did the calculation.*
>
> *Adding a new kind of assessment (fire safety, now) meant one new Inspector — not one new method on every business type.*
>
> *This is the Visitor. Define a new operation on elements of an object structure without changing the classes of those elements."*

---

> 💡 **Visitor Pattern**: Lets you add further operations to objects without modifying them. The visitor implements the operation; the element just accepts the visitor.
>
> ```
> interface ContractVisitor {
>   visitSimple(c: SimpleContract): void
>   visitDerivative(c: DerivativeContract): void
>   visitFutures(c: FuturesContract): void
> }
>
> interface Contract {
>   accept(visitor: ContractVisitor): void
> }
>
> class SimpleContract implements Contract {
>   accept(v) { v.visitSimple(this) }  // Knows only to call visitSimple
> }
>
> class TaxCalculatorVisitor implements ContractVisitor {
>   visitSimple(c)     { calculateFlatTax(c) }
>   visitDerivative(c) { calculateComplexTax(c) }
>   visitFutures(c)    { calculateFuturesTax(c) }
> }
>
> class AuditVisitor implements ContractVisitor {
>   visitSimple(c)     { logSimpleAudit(c) }
>   visitDerivative(c) { logDerivativeAudit(c) }
>   visitFutures(c)    { logFuturesAudit(c) }
> }
>
> // Add new operation: add one new Visitor class. No contract class changes.
> contracts.forEach(c => c.accept(new RiskAssessmentVisitor()))
> ```
>
> **Key benefit**: Add new operations to a set of classes without modifying them. Operations are grouped by what they do, not by what they operate on.
>
> **When to use**: Object structure is stable (few new types). Operations are frequent and varied.

---

### Pattern 23: The Interpreter — *Give Meaning to Language*

Merchants needed to define **custom trade rules** using a simple rule language they could understand, without needing to write code:

```
IF trade.amount > 10000 AND trade.kingdom == "Northern" THEN apply_tariff(HIGH)
IF trade.type == "futures" OR trade.type == "derivatives" THEN require_approval
```

The platform needed to parse these rules, understand them, and execute them.

---

> **Corvin's Entry: The Legal Code Parser**
>
> *"The city's legal code was written in a formal language with precise grammar. Not ordinary speech — a grammar where every sentence meant one exact thing. The Legal Interpreter's office had one job: read a legal statement, parse its structure, and determine its meaning. 'IF condition THEN consequence' had a fixed grammar; the Interpreter knew exactly how to evaluate it.*
>
> *This is the Interpreter. Given a language, define a representation for its grammar and an interpreter that uses it to interpret sentences."*

---

> 💡 **Interpreter Pattern**: Defines a grammar for a language and provides an interpreter that deals with that grammar. Used for simple languages or rule engines.
>
> ```
> interface Expression {
>   interpret(context: TradeContext): boolean
> }
>
> class AmountGreaterThan implements Expression {
>   threshold: Money
>   interpret(ctx) { return ctx.trade.amount > threshold }
> }
>
> class KingdomEquals implements Expression {
>   kingdom: String
>   interpret(ctx) { return ctx.trade.kingdom == kingdom }
> }
>
> class AndExpression implements Expression {
>   left, right: Expression
>   interpret(ctx) { return left.interpret(ctx) && right.interpret(ctx) }
> }
>
> class OrExpression implements Expression {
>   left, right: Expression
>   interpret(ctx) { return left.interpret(ctx) || right.interpret(ctx) }
> }
>
> // Rule: "amount > 10000 AND kingdom == Northern"
> Expression rule = new AndExpression(
>   new AmountGreaterThan(Money.of(10000)),
>   new KingdomEquals("Northern")
> )
> rule.interpret(tradeContext)  // true or false
> ```
>
> **Key benefit**: Rule logic is represented as composable objects, readable and modifiable at runtime.
>
> **Real-world**: SQL parsing, regular expression engines, configuration rule languages, template engines.

---

## EPILOGUE — THE TWENTY-FOURTH ENTRY

Six months after Priya had first sat down in the Library on the Hill, the Universal Trading Platform was live.

Seventeen kingdoms. Forty-two merchant types. One hundred legacy systems, all connected through Adapters. One Singleton rate registry. Forty template agreements cloned through Prototypes. Merchant hierarchies managed by Composites. Behaviors added through Decorators. State machines governing Agreement lifecycle. Observers notifying every interested party. Command objects enabling undo. A Visitor for each new compliance requirement. Rule engines built from Interpreters.

It worked.

Not just worked — it was *changeable*. When the Western Kingdom added a new tariff scheme, it was one new Strategy class. When a new report type was needed, one new Template Method subclass. When the Eastern Kingdom's API changed, one new Adapter.

Fen, the ancient librarian, found Priya at the reading stand on the morning after the launch.

*"You're back,"* he said.

*"I want to add something,"* she said.

She opened the Codex to a blank page at the end — the first blank page in two hundred years — and wrote:

---

> **Corvin's Entry (added by Priya, Year 15 of the New City):**
>
> *"After reading twenty-three entries, the student might ask: 'Which pattern do I use?'*
>
> *This is the twenty-fourth pattern. It has no name in the old books.*
>
> *Call it: Judgment.*
>
> *A pattern is not an answer. It is a question: 'Have I seen this shape before?'*
>
> *The Singleton is only correct when there must be one. The Observer is only correct when one change must notify many. The Decorator is only correct when capabilities must be composable. Pattern worship — reaching for Visitor when a simple method would do — is more dangerous than ignorance.*
>
> *Before applying any pattern, ask:*
> *What problem am I actually solving?*
> *Does this pattern solve that problem, or does it only look like it does?*
> *What is the cost of this pattern's complexity?*
> *Is there a simpler solution I'm avoiding because patterns feel more professional?*
>
> *Corvin wrote twenty-three patterns because he saw twenty-three problems appear again and again.*
> *He did not write them because every problem needs twenty-three solutions.*
>
> *The master builder uses patterns the way a master carpenter uses joints:*
> *not to show that she knows them — but because they are exactly right for the wood.*"

---

She closed the Codex.

Fen was gone. The Library was empty and quiet.

Outside the window, the river was flowing — the River that her father's payment had flowed through, the River that Dev had spent eleven years building, the River that she had spent one night repairing.

The city was trading.

She walked home.

---

## THE COMPLETE CODEX
### *All 23 Patterns — Their Shape, Their Purpose, Their Place*

| # | Pattern | Category | Problem It Solves | The Shape |
|---|---|---|---|---|
| 1 | **Singleton** | Creational | Need exactly one instance | Private constructor + static `getInstance()` |
| 2 | **Factory Method** | Creational | Create objects without specifying exact class | Interface for creation; subclass decides what |
| 3 | **Abstract Factory** | Creational | Create families of related compatible objects | Factory that creates a suite of related products |
| 4 | **Builder** | Creational | Complex object with many optional config steps | Fluent builder + `build()` validation |
| 5 | **Prototype** | Creational | Avoid expensive object creation; need many similar objects | Clone an existing instance; deep copy |
| 6 | **Adapter** | Structural | Two incompatible interfaces must work together | Wrapper that translates one interface to another |
| 7 | **Bridge** | Structural | Abstraction and implementation should vary independently | Abstraction holds reference to implementation; both extensible |
| 8 | **Composite** | Structural | Treat individual items and collections identically | Shared interface for leaf and container; container holds children |
| 9 | **Decorator** | Structural | Add behavior to objects dynamically without subclassing | Wrapper implementing same interface; delegates + adds behavior |
| 10 | **Facade** | Structural | Simplify complex subsystem for common use | One simple class that coordinates many complex ones |
| 11 | **Flyweight** | Structural | Many similar objects consuming too much memory | Shared intrinsic state; unique extrinsic state per instance |
| 12 | **Proxy** | Structural | Control access to expensive/sensitive object | Same interface as real object; adds access control or lazy init |
| 13 | **Chain of Responsibility** | Behavioral | Request processed by one of several handlers; caller doesn't know which | Handlers linked in a chain; each passes or handles |
| 14 | **Command** | Behavioral | Need to queue, undo, or log operations | Encapsulate request as object with execute() + undo() |
| 15 | **Iterator** | Behavioral | Traverse collection without knowing its structure | Standard hasNext()/next() interface over any collection |
| 16 | **Mediator** | Behavioral | Many objects communicating creates N² connections | Central object through which all communicate |
| 17 | **Memento** | Behavioral | Save and restore object state without exposing internals | Object produces opaque Memento; stored separately; restored on demand |
| 18 | **Observer** | Behavioral | One object's state change must notify many others | Subject maintains observer list; notifies all on change |
| 19 | **State** | Behavioral | Object behavior changes entirely based on its current state | Each state is an object that handles requests; object delegates to current state |
| 20 | **Strategy** | Behavioral | Multiple interchangeable algorithms for same task | Algorithm encapsulated in class; client chooses which |
| 21 | **Template Method** | Behavioral | Algorithm skeleton is fixed; specific steps vary | Base class defines skeleton; subclasses fill in abstract steps |
| 22 | **Visitor** | Behavioral | New operations on stable object hierarchy without modifying classes | Visitor implements op per type; elements call `accept(visitor)` |
| 23 | **Interpreter** | Behavioral | Define and evaluate a formal language or rule grammar | Each grammar rule is a class with `interpret()` method |

---

### The Pattern Selector — Which One Do You Need?

```
You're creating objects:
  └── Need exactly one instance?               → Singleton
  └── Don't know which class to instantiate?   → Factory Method
  └── Need a compatible family of objects?     → Abstract Factory
  └── Complex object with many optional steps? → Builder
  └── Creating same object repeatedly?         → Prototype

You're composing objects:
  └── Two incompatible interfaces?             → Adapter
  └── Abstraction and implementation diverging?→ Bridge
  └── Part-whole hierarchies (trees)?          → Composite
  └── Adding behavior without subclassing?     → Decorator
  └── Simplifying a complex subsystem?         → Facade
  └── Millions of similar objects / memory?    → Flyweight
  └── Controlling access or lazy loading?      → Proxy

You're defining behavior:
  └── Multiple handlers, one processes it?     → Chain of Responsibility
  └── Need queue / undo / log operations?      → Command
  └── Traversing collections uniformly?        → Iterator
  └── Too many direct connections between N?   → Mediator
  └── Save and restore state?                  → Memento
  └── One change must notify many?             → Observer
  └── Behavior changes with state?             → State
  └── Interchangeable algorithms?              → Strategy
  └── Fixed algorithm flow, varying steps?     → Template Method
  └── New operations on stable hierarchy?      → Visitor
  └── Evaluating a language or rule grammar?   → Interpreter
```

---

> *A junior engineer asks: "What pattern should I use here?"*
> *A senior engineer asks: "What problem am I actually facing?"*
> *And then finds the pattern with that shape.*
>
> *The Codex is not a menu.*
> *It is a mirror.*

---

*🖊️ Written for engineers who have read every pattern and still reached for the wrong one.*
*The patterns are not the knowledge.*
*Knowing which problem each one solves — that is the knowledge.*
