SELECT
    DB_NAME(s.database_id) AS DatabaseName,
    s.last_wait_type AS LastWaitType,
    c.client_net_address AS ClientAddress,
    s.host_name AS HostName,
    s.login_name AS LoginName,
    COUNT(*) OVER (PARTITION BY s.login_name) AS ConnectionCount
FROM
    sys.dm_exec_sessions s
LEFT JOIN
    sys.dm_exec_connections c ON s.session_id = c.session_id
WHERE
    s.is_user_process = 1; -- Exclude system processes