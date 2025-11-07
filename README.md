# Contr-le-Symfony

ECURIE :
- id
- NOM = str
- Pilotes.nom FK str
- moteur.marque KF str

PILOTE : 
- id
- nom str
- prenom str
- points base 12 int
- date-debut-F1 datetime
- Suspendu true/false Bool

Moteur :
- id
- marque (ecurie.nom) FK str

Infractions :
- id 
- penalités (points) int
- amendes (euros) int
- nom.course str
- pilote.nom str FK
- date.infraction datetime

Frédérick Rat

Create fixtures to add teams (minimum 3) and their drivers (minimum 3 drivers per team).
Add a route to modify a team's drivers.
Add a route to issue a fine and/or penalty to a team or driver.
Add a route listing all infractions, with filters by team, driver, and date.
Add a service that triggers when an infraction is added; if the driver has fewer than 1 point, their status is set to "suspended."
Add a basic JWT connection system to secure all these routes.
Add an ADMIN role; only this role has the right to issue infractions (fines or penalties).

php bin/console doctrine:fixtures:load

If not already existing : Add a service that triggers when an infraction is added; if the driver has fewer than 1 point, their status is set to "suspended."

security:
    enable_authenticator_manager: true
    # https://symfony.com/doc/current/security.html#registering-the-user-hashing-passwords
    password_hashers:
        Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface: 'auto'
    # https://symfony.com/doc/current/security.html#loading-the-user-the-user-provider
    providers:
        # used to reload user from session & other features (e.g. switch_user)
        app_user_provider:
            entity:
                class: App\Entity\User
                property: email
    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
        login:
            pattern: ^/api/login
            stateless: true
            provider: app_user_provider
            json_login:
                check_path: /api/login
                success_handler: lexik_jwt_authentication.handler.authentication_success
                failure_handler: lexik_jwt_authentication.handler.authentication_failure

        api_register:
            pattern: ^/api/user/create
            stateless: false
            provider: app_user_provider

        api:
            pattern: ^/api
            stateless: true
            provider: app_user_provider
            jwt: ~

            # activate different ways to authenticate
            # https://symfony.com/doc/current/security.html#the-firewall

            # https://symfony.com/doc/current/security/impersonating_user.html
            # switch_user: true

    # Easy way to control access for large sections of your site
    # Note: Only the *first* access control that matches will be used
    access_control:
        - { path: ^/api/user/create, roles: PUBLIC_ACCESS, methods: POST }
        - { path: ^/api/login, roles: PUBLIC_ACCESS , methods : POST}
        - { path: '^/api/category/create', roles: [ROLE_ADMIN], methods: POST }
        # Only admins can manage (create/update/delete) infractions
        - { path: '^/api/infraction/manage', roles: [ROLE_ADMIN] }
        - { path: ^/api,       roles: [IS_AUTHENTICATED_FULLY] }
        # - { path: '^/category/create', roles: [IS_AUTHENTICATED_FULLY, ROLE_USER], methods: POST }
        # - { path: '^/product/create', roles: [PUBLIC_ACCESS], methods: POST }
        # - { path: ^/api/login, roles: [PUBLIC_ACCESS] }
        # - { path: ^/admin, roles: ROLE_ADMIN }
        # - { path: ^/profile, roles: ROLE_USER }

    role_hierarchy:
        ROLE_ADMIN: [ROLE_MANAGER, ROLE_USER]
        ROLE_MANAGER: [ROLE_USER]

when@test:
    security:
        password_hashers:
            # By default, password hashers are resource intensive and take time. This is
            # important to generate secure password hashes. In tests however, secure hashes
            # are not important, waste resources and increase test times. The following
            # reduces the work factor to the lowest possible values.
            Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface:
                algorithm: auto
                cost: 4 # Lowest possible value for bcrypt
                time_cost: 3 # Lowest possible value for argon
                memory_cost: 10 # Lowest possible value for argon
