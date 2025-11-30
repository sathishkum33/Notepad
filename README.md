# Subquery to get Environment from Instance table
    env_subquery = Instance.objects.filter(
        Application__Application=application,
        Server__ComputeName=OuterRef('Server__ComputeName')
    ).values('Environment__name')[:1]

    # Base Server list (from Server or Instance model)
    server_list = Server.objects.values(
        "Application__Application",
        "Server__Hostname",
        "Server__RAM",
        "Server__CPU",
        "Server__Status",
        "Server__OS",
        "Server__ComputeName",
    ).annotate(
        Environment=Subquery(env_subquery),    # ← pulls env if exists, empty if not
        Usage_Type=Value("APP", output_field=CharField())
    )

    # Database list
    db_list = Database.objects.values(
        "Application__Application",
        "Server__Hostname",
        "Server__RAM",
        "Server__CPU",
        "Server__Status",
        "Server__OS",
        "Server__ComputeName",
    ).annotate(
        Environment=Subquery(env_subquery),     # reuses same logic
        Usage_Type=Value("DB", output_field=CharField())
    )

    # UNION both
    final_list = server_list.union(db_list).order_by("Environment", "Usage_Type")
