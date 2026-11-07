<p align="center">
  <img src="logo.svg" width="120" alt="GeoPS logo">
</p>

<h1 align="center">GeoPS</h1>

<p align="center">
  <b>Ofertas y cupones hiperlocales</b> que conectan negocios locales con consumidores cercanos.<br>
  <i>Hyperlocal offers and coupons connecting local businesses with nearby consumers.</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-MVP%20released-brightgreen" alt="Status">
  <img src="https://img.shields.io/badge/Java-21-red" alt="Java 21">
  <img src="https://img.shields.io/badge/Spring%20Boot-4-brightgreen" alt="Spring Boot 4">
  <img src="https://img.shields.io/badge/Flutter-Dart%203-02569B" alt="Flutter">
  <img src="https://img.shields.io/badge/PostgreSQL-PostGIS-336791" alt="PostGIS">
</p>

---

### 🇪🇸 Qué es GeoPS

Un consumidor abre el mapa y ve ofertas de negocios **a pocos metros**: la bodega de la esquina, la panadería del barrio, el restaurante de la cuadra. Reclama un cupón, lo muestra en el local y el negocio lo canjea. Los negocios publican ofertas en minutos y ven cuántos canjes generan.

### 🇬🇧 What GeoPS is

A consumer opens the map and sees offers from businesses **a few meters away**: the corner store, the neighborhood bakery, the restaurant down the block. They claim a coupon, show it at the counter and the business redeems it. Businesses publish offers in minutes and see how many redemptions they get.

## Repositories

| Repo | Description | Stack |
|---|---|---|
| [**geops-api**](https://github.com/geops-app/geops-api) | REST API: auth, offers by radius, coupons, redemption, stats | Java 21 · Spring Boot 4 · PostgreSQL + PostGIS · Redis · Flyway |
| [**geops-mobile**](https://github.com/geops-app/geops-mobile) | App for consumers and businesses | Flutter · Riverpod · go_router · Dio · Clean Architecture |

## Features (MVP)

- **Consumers**: offers within a chosen radius on a map or a list, filtered by category; claim a coupon and show it as a QR code.
- **Businesses**: profile and location, publish and pause offers, issue coupons with stock, redeem codes at the counter and see 7-day stats.

## Architecture

```mermaid
flowchart LR
    subgraph Mobile["📱 geops-mobile (Flutter)"]
        UI[Screens] --> State[Riverpod]
        State --> Repo[Repositories]
        Repo --> Http[Dio + auth interceptor]
        Repo --> Secure[(Secure storage)]
    end

    subgraph Api["☕ geops-api (Spring Boot)"]
        Sec[Spring Security + JWT] --> Ctx[Bounded contexts<br/>identity · offers · coupons]
    end

    Http -- "HTTPS /api/v1" --> Sec
    Ctx --> PG[(PostgreSQL + PostGIS)]
    Ctx --> RD[(Redis<br/>cache · rate limit)]
```

## Roadmap

| Version | Scope | Status |
|---|---|---|
| **v0.1.0** | Foundation, CI, authentication with roles | ✅ Released |
| **v0.2.0** | Businesses, offers, map and geolocation | ✅ Released |
| **v0.3.0** | Coupons, redemption, stats, deployment (**MVP**) | ✅ Released |
| Next | QR scanning, favorites, reviews, cart, subscriptions, payments (Stripe test mode), notifications | ⏳ Planned |

## Engineering practices

- **Git flow**: `feature/*` → PR → `develop` → release PR → `main` + semantic tag. Protected branches with required CI.
- **Security by design**: BCrypt, short-lived JWT + rotated refresh tokens stored as hashes, rate limiting, restrictive CORS, row locks so a coupon is never redeemed twice, an audit log, secrets only in environment variables.
- **Testing**: JUnit 5 + Testcontainers (real PostGIS and Redis) in the API; unit, widget and flow tests in the app.
- **Deployment**: Docker on Render (API + Redis) and Neon (PostgreSQL + PostGIS), deployed from `main` only after CI passes.

<sub>Built by <a href="https://github.com/IDymian">@IDymian</a>.</sub>
