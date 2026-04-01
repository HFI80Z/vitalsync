# VitalSync

Application de suivi medical et sportif.

## Architecture

Le projet est compose de 3 services :
- **Backend** : API REST Node.js/Express (port 3000)
- **Frontend** : interface web servie par Nginx (port 80)
- **Database** : PostgreSQL 16

Le frontend communique avec le backend via un proxy
Nginx (proxy_pass sur /api/). Le backend se connecte
a PostgreSQL pour stocker les donnees.

## Schema d'architecture

```
Utilisateur --> Nginx (frontend:80)
                  |
                  |--> /api/* --> Backend (Node:3000)
                                    |
                                    --> PostgreSQL (5432)
```

## Pipeline CI/CD

La pipeline GitHub Actions se declenche sur push
sur develop et PR vers main. Elle comporte 3 jobs :
1. **test** : npm install + npm test (Jest)
2. **build-and-push** : build des images Docker
   et push sur GHCR avec tag SHA du commit
3. **deploy-staging** : docker compose up + health
   check sur /health

## Choix techniques

- **Node.js 20 Alpine** : image legere pour le backend
- **Multi-stage build** : separe les deps de test et
  de prod pour une image plus petite et securisee
- **GHCR** : registry integre a GitHub, gratuit, pas
  de rate limiting comme Docker Hub
- **Reseau bridge dedie** : isole les conteneurs
  VitalSync des autres conteneurs sur la machine
- **Volume persistant** : les donnees PostgreSQL
  survivent au redemarrage des conteneurs
- **GitHub Actions** : CI/CD integre a GitHub
