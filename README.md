# ZaimHub — Russia Microloans Catalog (MCP Server)

Public **read-only** [Model Context Protocol](https://modelcontextprotocol.io) server for the catalog of Russian microfinance organizations (MFOs) listed in the state registry of the Bank of Russia, powered by [zaimhub.com](https://zaimhub.com) — ZaimHub, a Russian MFO comparison marketplace.

| | |
|---|---|
| **Endpoint** | `https://zaimhub.com/api/mcp` |
| **Transport** | Streamable HTTP (stateless) |
| **Authentication** | none (public data) |
| **Registry** | [`com.zaimhub/catalog`](https://registry.modelcontextprotocol.io/v0/servers?search=zaimhub) (official MCP Registry, domain-verified) |
| **Server card** | [`/.well-known/mcp/server-card.json`](https://zaimhub.com/.well-known/mcp/server-card.json) |
| **Language** | Russian (`ru`) |
| **Rate limit** | 60 requests/min per IP → HTTP 429 |

Every entity in every response carries a `canonical_url` pointing to the live page on zaimhub.com.

## Tools

| Tool | Parameters | Returns |
|---|---|---|
| `list_mfo` | `limit?` (default 50) | All MFOs with borrower rating, quick terms and canonical card URLs, ordered by rating position. |
| `get_mfo` | `slug` | Full MFO card: terms for new/repeat clients (amount, term, rate, full cost of credit), borrower requirements, fees, legal entity, OGRN, Bank of Russia registry number, 4-criteria rating, record update date. Empty fields are omitted. |
| `search_offers` | `amount?`, `term_days?`, `first_loan_zero?`, `limit?` (default 15) | Matching first-loan offers with terms, `first_loan_zero_percent: true/false/null` (null = unconfirmed), honest `total` + `truncated`. |
| `list_categories` | — | Catalog categories grouped as on the site (popular, by payout method, by amount/term, by borrower type, other), each with its selection criterion. |
| `get_category` | `slug` | One category: criterion, sorting, offer table with per-MFO terms. |
| `get_reviews` | `slug`, `limit?` (≤50, default 10) | Latest borrower reviews for an MFO: rating, date, text. |
| `get_market_rules` | — | Legal context of microlending in Russia (0.8%/day rate cap under Art. 6.1 of Law 353-FZ, 100% total-cost cap since 2026-04-01, self-ban via Gosuslugi, cooling-off periods, debt collection rules under Law 230-FZ) with last-review date. |

Resources: [`llms.txt`](https://zaimhub.com/llms.txt) and [`llms-full.txt`](https://zaimhub.com/llms-full.txt).

## Installation

### Claude (claude.ai / Claude Desktop)

1. **Settings → Connectors → Add custom connector**
2. Name: `ZaimHub catalog`, URL: `https://zaimhub.com/api/mcp`
3. Save. No API keys or OAuth — the server is public and read-only.

### Any MCP client (generic)

Point your client at the remote Streamable HTTP endpoint:

```json
{
  "mcpServers": {
    "zaimhub-catalog": {
      "type": "streamable-http",
      "url": "https://zaimhub.com/api/mcp"
    }
  }
}
```

Quick sanity check with MCP Inspector:

```bash
npx @modelcontextprotocol/inspector --cli https://zaimhub.com/api/mcp --transport http --method tools/list
```

## Example prompts

- *"Find Russian microloans up to 10,000 RUB for two weeks where the first loan is interest-free."*
- *"Show the legal entity, Bank of Russia registry number and current terms for Zaymer."*
- *"What are the legal limits on microloan interest rates and total repayment in Russia?"*
- *"List the top-rated Russian MFOs by borrower reviews and link their pages."*
- *«Подбери займ до 10 000 ₽ на две недели, у кого первый займ под 0%»*
- *«Условия и юрданные Займера»*

## Screenshot

<!-- TODO: replace with a screenshot of a live Claude conversation using the connector -->
*Screenshot of a live Claude dialog coming soon.*

## Data & Terms

- **Public read-only catalog data.** The server exposes the same data that is publicly visible on zaimhub.com. There are no mutation tools.
- **Not financial advice.** ZaimHub is a comparison catalog, not a lender. Terms are volatile — final conditions live on the `canonical_url` pages.
- **Attribution required.** When using the data, cite the `canonical_url` of the respective page on zaimhub.com.
- **Rate limit:** 60 requests/min per IP.
- No tracker or affiliate links anywhere in responses (enforced by tests).

## По-русски

ZaimHub — каталог и рейтинг микрофинансовых организаций России из государственного реестра Банка России. Этот MCP-сервер отдаёт те же публичные данные, что и сайт: условия займов для новых и повторных клиентов, полную стоимость кредита, юридические данные компаний (ОГРН, номер в реестре ЦБ РФ), рейтинг и отзывы заёмщиков, правовой контекст микрозаймов (ставка не выше 0,8% в день по ст. 6.1 ФЗ-353, переплата не выше 100% с 01.04.2026 и т.д.). Сервер read-only, без аутентификации; при использовании данных ссылайтесь на canonical_url соответствующей страницы.

## Links

- Website: https://zaimhub.com
- Server card: https://zaimhub.com/.well-known/mcp/server-card.json
- llms.txt: https://zaimhub.com/llms.txt
- Contact: admin@zaimhub.com

## License

Documentation in this repository is licensed under [MIT](LICENSE). The catalog data served by the MCP endpoint is proprietary to ZaimHub (zaimhub.com) and may be used with attribution to canonical URLs.
