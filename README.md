SELECT 
    RAMO, 
    A.Poliza, 
    B.SumaPrimaNetaPositiva, 
    CASE 
        WHEN B.ConteoPolizas > 1 THEN 0 
        ELSE B.SumaPrimaNetaNegativa 
    END AS SumaPrimaNetaNegativa, 
    B.[PRIMA NETA ENDOSO]
FROM 
    (SELECT 
        RAMO, 
        POLIZA, 
        'MENSUAL' AS 'FORMA DE PAGO', 
        '-' AS 'Prima de Renovación o Primer año', 
        CC, 
        [Descripcion] 
     FROM 
        C_POLIZA_RESUMEN 
     WHERE 
        Poliza IN (50, 16190027)
    ) A 
INNER JOIN 
    (SELECT 
        POLIZA, 
        SUM(CAST([PRIMA NETA ENDOSO] AS FLOAT)) AS [PRIMA NETA ENDOSO], 
        SUM(CASE WHEN CAST([PRIMA NETA ENDOSO] AS FLOAT) > 0 THEN CAST([PRIMA NETA ENDOSO] AS FLOAT) ELSE 0 END) as SumaPrimaNetaPositiva, 
        SUM(CASE WHEN CAST([PRIMA NETA ENDOSO] AS FLOAT) < 0 THEN CAST([PRIMA NETA ENDOSO] AS FLOAT) ELSE 0 END) as SumaPrimaNetaNegativa,
        COUNT(POLIZA) AS ConteoPolizas 
     FROM 
        TAB_ENSOSOS2023_PRUEBA 
     WHERE 
        Poliza IN (50, 16190027)
     GROUP BY 
        POLIZA
    ) B ON A.POLIZA = B.POLIZA
