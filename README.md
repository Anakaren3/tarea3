SELECT 
    RAMO, 
    A.Poliza, 
    B.SumaPrimaNetaPositiva, 
    CASE 
        WHEN COUNT(*) OVER(PARTITION BY A.Poliza) > 1 THEN NULL 
        ELSE B.SumaPrimaNetaNegativa 
    END AS SumaPrimaNetaNegativa, 
    B.[PRIMA NETA ENDOSO]
FROM 
(
    SELECT 
        RAMO, 
        POLIZA, 
        'MENSUAL' AS [FORMA DE PAGO], 
        CC, 
        [Descripcion ] 
    FROM 
        C_POLIZA_RESUMEN 
    WHERE 
        Poliza IN (50, 16190027)
) A 
INNER JOIN 
(
    SELECT 
        POLIZA, 
        SUM(CAST([PRIMA NETA ENDOSO] AS FLOAT)) AS [PRIMA NETA ENDOSO],
        SUM(CASE WHEN CAST([PRIMA NETA ENDOSO] AS FLOAT) >= 0 THEN CAST([PRIMA NETA ENDOSO] AS FLOAT) ELSE 0 END) AS SumaPrimaNetaPositiva,
        SUM(CASE WHEN CAST([PRIMA NETA ENDOSO] AS FLOAT) < 0 THEN CAST([PRIMA NETA ENDOSO] AS FLOAT) ELSE 0 END) AS SumaPrimaNetaNegativa 
    FROM 
        TAB_ENSOSOS2023_PRUEBA 
    WHERE 
        Poliza IN (50, 16190027)
    GROUP BY 
        POLIZA
) B ON A.POLIZA = B.POLIZA
