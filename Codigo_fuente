import React, { useState } from 'react';
import { Upload, Download, Calculator, AlertCircle, CheckCircle, FileText } from 'lucide-react';
import * as XLSX from 'xlsx';

const RouteOptimizer = () => {
  const [pdvFile, setPdvFile] = useState(null);
  const [usuariosFile, setUsuariosFile] = useState(null);
  const [results, setResults] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [metrics, setMetrics] = useState(null);

  // Validación de esquema PDV
  const validatePDVSchema = (data) => {
    const requiredColumns = [
      'ID', 'Nombre del PDV', 'Segmentación', 'Cadena', 'Canal del PDV',
      'Regional', 'País', 'Dirección', 'Latitud', 'Longitud', 'Activo',
      'Visitas semanales', 'Duración visita(horas)', 'Prioridad'
    ];
    
    if (!data || data.length === 0) {
      throw new Error('El archivo PDV está vacío');
    }

    const columns = Object.keys(data[0]);
    const missing = requiredColumns.filter(col => !columns.includes(col));
    
    if (missing.length > 0) {
      throw new Error(`Columnas faltantes en PDV: ${missing.join(', ')}`);
    }

    // Validar tipos de datos
    data.forEach((row, idx) => {
      const lat = parseFloat(String(row.Latitud).replace(/\t/g, ''));
      const lng = parseFloat(String(row.Longitud).replace(/\t/g, ''));
      
      if (isNaN(lat) || isNaN(lng)) {
        throw new Error(`Fila ${idx + 1}: Latitud o Longitud inválida`);
      }
      
      if (row.Activo !== 'Sí' && row.Activo !== 'No') {
        throw new Error(`Fila ${idx + 1}: Campo 'Activo' debe ser 'Sí' o 'No'`);
      }
    });

    return true;
  };

  // Validación de esquema Usuarios
  const validateUsuariosSchema = (data) => {
    const requiredColumns = [
      'ID', 'Nombre del empleado', 'Usuario', 'Usuario activo',
      'Perfil de acceso', 'País', 'Regionales', 'E-mail',
      'Latitud', 'Longitud', 'Fecha de admisión',
      'Horas por día usuario', 'Horas por semana usuario', 'Visitas maximas por dia'
    ];
    
    if (!data || data.length === 0) {
      throw new Error('El archivo de Usuarios está vacío');
    }

    const columns = Object.keys(data[0]);
    const missing = requiredColumns.filter(col => !columns.includes(col));
    
    if (missing.length > 0) {
      throw new Error(`Columnas faltantes en Usuarios: ${missing.join(', ')}`);
    }

    // Validar tipos de datos
    data.forEach((row, idx) => {
      if (isNaN(row.Latitud) || isNaN(row.Longitud)) {
        throw new Error(`Fila ${idx + 1}: Latitud o Longitud inválida`);
      }
      
      if (row['Usuario activo'] !== 'Sí' && row['Usuario activo'] !== 'No') {
        throw new Error(`Fila ${idx + 1}: Campo 'Usuario activo' debe ser 'Sí' o 'No'`);
      }
    });

    return true;
  };

  // Fórmula Haversine para calcular distancia
  const haversineDistance = (lat1, lon1, lat2, lon2) => {
    const R = 6371; // Radio de la Tierra en km
    const dLat = (lat2 - lat1) * Math.PI / 180;
    const dLon = (lon2 - lon1) * Math.PI / 180;
    const a = 
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
      Math.sin(dLon / 2) * Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
  };

  // Calcular tiempo de viaje
  const calculateTravelTime = (distKm, speedKmh = 40) => {
    return (distKm / speedKmh) * 60; // minutos
  };

  // Algoritmo Nearest Neighbor
  const nearestNeighbor = (startLat, startLon, pdvs) => {
    if (pdvs.length === 0) return [];
    
    const route = [];
    const unvisited = [...pdvs];
    let currentLat = startLat;
    let currentLon = startLon;

    while (unvisited.length > 0) {
      let nearestIdx = 0;
      let minDist = Infinity;

      unvisited.forEach((pdv, idx) => {
        const pdvLat = parseFloat(String(pdv.Latitud).replace(/\t/g, ''));
        const pdvLon = parseFloat(String(pdv.Longitud).replace(/\t/g, ''));
        const dist = haversineDistance(currentLat, currentLon, pdvLat, pdvLon);
        
        if (dist < minDist) {
          minDist = dist;
          nearestIdx = idx;
        }
      });

      const nearest = unvisited.splice(nearestIdx, 1)[0];
      route.push(nearest);
      currentLat = parseFloat(String(nearest.Latitud).replace(/\t/g, ''));
      currentLon = parseFloat(String(nearest.Longitud).replace(/\t/g, ''));
    }

    return route;
  };

  // Mejora 2-opt
  const twoOptImprovement = (route, startLat, startLon) => {
    if (route.length < 4) return route;
    
    let improved = true;
    let bestRoute = [...route];
    
    while (improved) {
      improved = false;
      
      for (let i = 1; i < bestRoute.length - 2; i++) {
        for (let j = i + 1; j < bestRoute.length; j++) {
          const newRoute = [...bestRoute];
          
          // Reverse segment
          const segment = newRoute.slice(i, j + 1).reverse();
          newRoute.splice(i, segment.length, ...segment);
          
          // Calculate distances
          const oldDist = calculateRouteDistance(bestRoute, startLat, startLon);
          const newDist = calculateRouteDistance(newRoute, startLat, startLon);
          
          if (newDist < oldDist) {
            bestRoute = newRoute;
            improved = true;
          }
        }
      }
    }
    
    return bestRoute;
  };

  // Calcular distancia total de una ruta
  const calculateRouteDistance = (route, startLat, startLon) => {
    if (route.length === 0) return 0;
    
    let totalDist = 0;
    let currentLat = startLat;
    let currentLon = startLon;
    
    route.forEach(pdv => {
      const pdvLat = parseFloat(String(pdv.Latitud).replace(/\t/g, ''));
      const pdvLon = parseFloat(String(pdv.Longitud).replace(/\t/g, ''));
      totalDist += haversineDistance(currentLat, currentLon, pdvLat, pdvLon);
      currentLat = pdvLat;
      currentLon = pdvLon;
    });
    
    return totalDist;
  };

  // Asignar PDVs a usuarios
  const assignPDVsToUsers = (pdvs, users) => {
    const activePDVs = pdvs.filter(p => p.Activo === 'Sí');
    const activeUsers = users.filter(u => u['Usuario activo'] === 'Sí');
    
    if (activeUsers.length === 0) {
      throw new Error('No hay usuarios activos');
    }

    // Ordenar PDVs por prioridad
    const sortedPDVs = activePDVs.sort((a, b) => a.Prioridad - b.Prioridad);
    
    // Distribuir PDVs entre usuarios de manera balanceada
    const assignments = activeUsers.map(user => ({
      user,
      pdvs: [],
      totalVisits: 0,
      totalHours: 0
    }));

    sortedPDVs.forEach((pdv, idx) => {
      // Asignar al usuario con menos carga
      assignments.sort((a, b) => a.totalVisits - b.totalVisits);
      const assignment = assignments[0];
      
      // Verificar restricciones
      const maxVisitsPerDay = assignment.user['Visitas maximas por dia'];
      const hoursPerDay = assignment.user['Horas por día usuario'];
      const visitDuration = pdv['Duración visita(horas)'];
      
      if (assignment.totalHours + visitDuration <= hoursPerDay) {
        assignment.pdvs.push(pdv);
        assignment.totalVisits++;
        assignment.totalHours += visitDuration;
      }
    });

    return assignments.filter(a => a.pdvs.length > 0);
  };

  // Procesar y calcular rutas
  const calculateRoutes = async () => {
    try {
      setLoading(true);
      setError(null);
      setResults(null);

      if (!pdvFile || !usuariosFile) {
        throw new Error('Por favor, sube ambos archivos (PDV y Usuarios)');
      }

      // Leer archivos
      const pdvData = await readExcelFile(pdvFile);
      const usuariosData = await readExcelFile(usuariosFile);

      // Validar esquemas
      validatePDVSchema(pdvData);
      validateUsuariosSchema(usuariosData);

      // Asignar PDVs a usuarios
      const assignments = assignPDVsToUsers(pdvData, usuariosData);

      // Optimizar rutas para cada usuario
      const optimizedRoutes = assignments.map(assignment => {
        const userLat = assignment.user.Latitud;
        const userLon = assignment.user.Longitud;

        // Nearest Neighbor
        let route = nearestNeighbor(userLat, userLon, assignment.pdvs);
        
        // Mejora 2-opt
        route = twoOptImprovement(route, userLat, userLon);

        // Calcular detalles de ruta
        let currentLat = userLat;
        let currentLon = userLon;
        let accumulatedDist = 0;
        let accumulatedTime = 0;
        let currentTime = 8 * 60; // Inicio a las 8:00 AM (en minutos)

        const routeDetails = route.map((pdv, idx) => {
          const pdvLat = parseFloat(String(pdv.Latitud).replace(/\t/g, ''));
          const pdvLon = parseFloat(String(pdv.Longitud).replace(/\t/g, ''));
          
          const distance = haversineDistance(currentLat, currentLon, pdvLat, pdvLon);
          const travelTime = calculateTravelTime(distance);
          const visitDuration = pdv['Duración visita(horas)'] * 60; // a minutos

          accumulatedDist += distance;
          accumulatedTime += travelTime;

          const arrivalTime = currentTime + travelTime;
          const departureTime = arrivalTime + visitDuration;

          const detail = {
            secuencia: idx + 1,
            pdv_id: pdv.ID,
            pdv_nombre: pdv['Nombre del PDV'],
            segmentacion: pdv.Segmentación,
            cadena: pdv.Cadena,
            distancia_km: distance.toFixed(2),
            tiempo_viaje_min: travelTime.toFixed(0),
            hora_llegada: formatTime(arrivalTime),
            hora_salida: formatTime(departureTime),
            duracion_visita_horas: pdv['Duración visita(horas)'],
            distancia_acumulada_km: accumulatedDist.toFixed(2),
            tiempo_acumulado_min: accumulatedTime.toFixed(0),
            prioridad: pdv.Prioridad
          };

          currentLat = pdvLat;
          currentLon = pdvLon;
          currentTime = departureTime;
          accumulatedTime += visitDuration;

          return detail;
        });

        return {
          usuario_id: assignment.user.ID,
          usuario_nombre: assignment.user['Nombre del empleado'],
          usuario_email: assignment.user['E-mail'],
          pdvs_asignados: route.length,
          distancia_total_km: accumulatedDist.toFixed(2),
          tiempo_total_min: accumulatedTime.toFixed(0),
          ruta: routeDetails
        };
      });

      // Calcular métricas globales
      const totalDistance = optimizedRoutes.reduce((sum, r) => sum + parseFloat(r.distancia_total_km), 0);
      const totalTime = optimizedRoutes.reduce((sum, r) => sum + parseFloat(r.tiempo_total_min), 0);
      const totalPDVs = optimizedRoutes.reduce((sum, r) => sum + r.pdvs_asignados, 0);

      setMetrics({
        usuarios_activos: optimizedRoutes.length,
        pdvs_atendidos: totalPDVs,
        distancia_total_km: totalDistance.toFixed(2),
        tiempo_total_horas: (totalTime / 60).toFixed(2),
        promedio_pdvs_por_usuario: (totalPDVs / optimizedRoutes.length).toFixed(1)
      });

      setResults(optimizedRoutes);
      setLoading(false);

    } catch (err) {
      setError(err.message);
      setLoading(false);
    }
  };

  // Formatear tiempo en HH:MM
  const formatTime = (minutes) => {
    const hours = Math.floor(minutes / 60);
    const mins = Math.floor(minutes % 60);
    return `${String(hours).padStart(2, '0')}:${String(mins).padStart(2, '0')}`;
  };

  // Leer archivo Excel
  const readExcelFile = (file) => {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = (e) => {
        try {
          const data = new Uint8Array(e.target.result);
          const workbook = XLSX.read(data, { type: 'array' });
          const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
          const jsonData = XLSX.utils.sheet_to_json(firstSheet);
          resolve(jsonData);
        } catch (err) {
          reject(new Error('Error al leer el archivo Excel'));
        }
      };
      reader.onerror = () => reject(new Error('Error al cargar el archivo'));
      reader.readAsArrayBuffer(file);
    });
  };

  // Descargar plantilla PDV
  const downloadPDVTemplate = () => {
    const template = [
      {
        'ID': 1,
        'Nombre del PDV': 'Ejemplo PDV 1',
        'Segmentación': 'AA',
        'Cadena': 'Cadena Ejemplo',
        'Canal del PDV': 'Retail',
        'Regional': 'Republica Dominicana',
        'País': 'Republica Dominicana',
        'Dirección': 'Dirección Ejemplo',
        'Latitud': 18.481597,
        'Longitud': -69.959925,
        'Activo': 'Sí',
        'Visitas semanales': 4,
        'Duración visita(horas)': 2,
        'Prioridad': 1
      }
    ];
    
    const ws = XLSX.utils.json_to_sheet(template);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, 'PDV');
    XLSX.writeFile(wb, 'Plantilla_PDV.xlsx');
  };

  // Descargar plantilla Usuarios
  const downloadUsuariosTemplate = () => {
    const template = [
      {
        'ID': 1,
        'Nombre del empleado': 'Ejemplo Usuario',
        'Usuario': 'ejemplo.usuario',
        'Usuario activo': 'Sí',
        'Perfil de acceso': 'Promotor',
        'País': 'Republica Dominicana',
        'Regionales': 'Republica Dominicana',
        'E-mail': 'ejemplo@email.com',
        'Latitud': 18.523408,
        'Longitud': -69.897495,
        'Fecha de admisión': '2020-01-01',
        'Horas por día usuario': 9,
        'Horas por semana usuario': 44,
        'Visitas maximas por dia': 5
      }
    ];
    
    const ws = XLSX.utils.json_to_sheet(template);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, 'Usuarios');
    XLSX.writeFile(wb, 'Plantilla_Usuarios.xlsx');
  };

  // Descargar resultados
  const downloadResults = () => {
    if (!results) return;

    const flatResults = [];
    results.forEach(userRoute => {
      userRoute.ruta.forEach(detail => {
        flatResults.push({
          'Usuario ID': userRoute.usuario_id,
          'Usuario Nombre': userRoute.usuario_nombre,
          'Email': userRoute.usuario_email,
          'Secuencia': detail.secuencia,
          'PDV ID': detail.pdv_id,
          'PDV Nombre': detail.pdv_nombre,
          'Segmentación': detail.segmentacion,
          'Cadena': detail.cadena,
          'Distancia (km)': detail.distancia_km,
          'Tiempo Viaje (min)': detail.tiempo_viaje_min,
          'Hora Llegada': detail.hora_llegada,
          'Hora Salida': detail.hora_salida,
          'Duración Visita (h)': detail.duracion_visita_horas,
          'Distancia Acumulada (km)': detail.distancia_acumulada_km,
          'Tiempo Acumulado (min)': detail.tiempo_acumulado_min,
          'Prioridad': detail.prioridad
        });
      });
    });

    const ws = XLSX.utils.json_to_sheet(flatResults);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, 'Resultados');
    XLSX.writeFile(wb, 'Resultados_Rutas_Optimizadas.xlsx');
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
      <div className="max-w-7xl mx-auto">
        {/* Header */}
        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
          <h1 className="text-3xl font-bold text-gray-800 mb-2">
            Optimizador de Rutas VRP
          </h1>
          <p className="text-gray-600">
            Sistema de optimización de rutas para visitadores con algoritmo Nearest Neighbor + 2-opt
          </p>
        </div>

        {/* Plantillas */}
        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
          <h2 className="text-xl font-semibold text-gray-800 mb-4 flex items-center gap-2">
            <Download className="w-5 h-5" />
            Descargar Plantillas
          </h2>
          <div className="flex gap-4">
            <button
              onClick={downloadPDVTemplate}
              className="flex items-center gap-2 px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors"
            >
              <FileText className="w-4 h-4" />
              Plantilla PDV
            </button>
            <button
              onClick={downloadUsuariosTemplate}
              className="flex items-center gap-2 px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors"
            >
              <FileText className="w-4 h-4" />
              Plantilla Usuarios
            </button>
          </div>
        </div>

        {/* Upload Section */}
        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
          <h2 className="text-xl font-semibold text-gray-800 mb-4 flex items-center gap-2">
            <Upload className="w-5 h-5" />
            Cargar Archivos
          </h2>
          
          <div className="grid md:grid-cols-2 gap-4 mb-4">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-2">
                Archivo PDV (Excel)
              </label>
              <input
                type="file"
                accept=".xlsx,.xls"
                onChange={(e) => setPdvFile(e.target.files[0])}
                className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              />
              {pdvFile && (
                <p className="text-sm text-green-600 mt-1 flex items-center gap-1">
                  <CheckCircle className="w-4 h-4" />
                  {pdvFile.name}
                </p>
              )}
            </div>

            <div>
              <label className="block text-sm font-medium text-gray-700 mb-2">
                Archivo Usuarios (Excel)
              </label>
              <input
                type="file"
                accept=".xlsx,.xls"
                onChange={(e) => setUsuariosFile(e.target.files[0])}
                className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              />
              {usuariosFile && (
                <p className="text-sm text-green-600 mt-1 flex items-center gap-1">
                  <CheckCircle className="w-4 h-4" />
                  {usuariosFile.name}
                </p>
              )}
            </div>
          </div>

          <button
            onClick={calculateRoutes}
            disabled={loading || !pdvFile || !usuariosFile}
            className="w-full md:w-auto px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 disabled:bg-gray-400 disabled:cursor-not-allowed transition-colors flex items-center justify-center gap-2"
          >
            <Calculator className="w-5 h-5" />
            {loading ? 'Calculando...' : 'Calcular Rutas Óptimas'}
          </button>
        </div>

        {/* Error Message */}
        {error && (
          <div className="bg-red-50 border border-red-200 rounded-lg p-4 mb-6 flex items-start gap-3">
            <AlertCircle className="w-5 h-5 text-red-600 flex-shrink-0 mt-0.5" />
            <div>
              <h3 className="font-semibold text-red-800">Error</h3>
              <p className="text-red-700">{error}</p>
            </div>
          </div>
        )}

        {/* Metrics */}
        {metrics && (
          <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-gray-800 mb-4">
              Métricas Globales
            </h2>
            <div className="grid grid-cols-2 md:grid-cols-5 gap-4">
              <div className="bg-blue-50 p-4 rounded-lg">
                <p className="text-sm text-gray-600">Usuarios Activos</p>
                <p className="text-2xl font-bold text-blue-600">{metrics.usuarios_activos}</p>
              </div>
              <div className="bg-green-50 p-4 rounded-lg">
                <p className="text-sm text-gray-600">PDVs Atendidos</p>
                <p className="text-2xl font-bold text-green-600">{metrics.pdvs_atendidos}</p>
              </div>
              <div className="bg-purple-50 p-4 rounded-lg">
                <p className="text-sm text-gray-600">Distancia Total</p>
                <p className="text-2xl font-bold text-purple-600">{metrics.distancia_total_km} km</p>
              </div>
              <div className="bg-orange-50 p-4 rounded-lg">
                <p className="text-sm text-gray-600">Tiempo Total</p>
                <p className="text-2xl font-bold text-orange-600">{metrics.tiempo_total_horas} h</p>
              </div>
              <div className="bg-indigo-50 p-4 rounded-lg">
                <p className="text-sm text-gray-600">Promedio PDVs</p>
                <p className="text-2xl font-bold text-indigo-600">{metrics.promedio_pdvs_por_usuario}</p>
              </div>
            </div>
          </div>
        )}

        {/* Results */}
        {results && (
          <div className="bg-white rounded-lg shadow-lg p-6">
            <div className="flex justify-between items-center mb-4">
              <h2 className="text-xl font-semibold text-gray-800">
                Rutas Optimizadas por Usuario
              </h2>
              <button
                onClick={downloadResults}
                className="flex items-center gap-2 px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors"
              >
                <Download className="w-4 h-4" />
                Descargar Resultados
              </button>
            </div>

            <div className="space-y-6">
              {results.map((userRoute, idx) => (
                <div key={idx} className="border border-gray-200 rounded-lg p-4">
                  <div className="bg-gradient-to-r from-blue-500 to-indigo-500 text-white p-4 rounded-t-lg -mx-4 -mt-4 mb-4">
                    <h3 className="text-lg font-semibold">
                      {userRoute.usuario_nombre} (ID: {userRoute.usuario_id})
                    </h3>
                    <p className="text-sm text-blue-100">{userRoute.usuario_email}</p>
                    <div className="grid grid-cols-3 gap-4 mt-3 text-sm">
                      <div>
                        <span className="opacity-80">PDVs:</span>{' '}
                        <span className="font-semibold">{userRoute.pdvs_asignados}</span>
                      </div>
                      <div>
                        <span className="opacity-80">Distancia:</span>{' '}
                        <span className="font-semibold">{userRoute.distancia_total_km} km</span>
                      </div>
                      <div>
                        <span className="opacity-80">Tiempo:</span>{' '}
                        <span className="font-semibold">{(parseFloat(userRoute.tiempo_total_min) / 60).toFixed(1)} h</span>
                      </div>
                    </div>
                  </div>

                  <div className="overflow-x-auto">
                    <table className="w-full text-sm">
                      <thead>
                        <tr className="bg-gray-50 border-b">
                          <th className="text-left p-2 font-semibold text-gray-700">#</th>
                          <th className="text-left p-2 font-semibold text-gray-700">PDV</th>
                          <th className="text-left p-2 font-semibold text-gray-700">Segmentación</th>
                          <th className="text-left p-2 font-semibold text-gray-700">Cadena</th>
                          <th className="text-right p-2 font-semibold text-gray-700">Dist (km)</th>
                          <th className="text-right p-2 font-semibold text-gray-700">Viaje (min)</th>
                          <th className="text-center p-2 font-semibold text-gray-700">Llegada</th>
                          <th className="text-center p-2 font-semibold text-gray-700">Salida</th>
                          <th className="text-right p-2 font-semibold text-gray-700">Visita (h)</th>
                          <th className="text-center p-2 font-semibold text-gray-700">Prioridad</th>
                        </tr>
                      </thead>
                      <tbody>
                        {userRoute.ruta.map((detail, detailIdx) => (
                          <tr key={detailIdx} className="border-b hover:bg-gray-50">
                            <td className="p-2">
                              <span className="inline-flex items-center justify-center w-6 h-6 bg-blue-100 text-blue-700 rounded-full text-xs font-semibold">
                                {detail.secuencia}
                              </span>
                            </td>
                            <td className="p-2 font-medium text-gray-800">{detail.pdv_nombre}</td>
                            <td className="p-2">
                              <span className="px-2 py-1 bg-purple-100 text-purple-700 rounded text-xs font-medium">
                                {detail.segmentacion}
                              </span>
                            </td>
                            <td className="p-2 text-gray-600">{detail.cadena}</td>
                            <td className="p-2 text-right text-gray-800">{detail.distancia_km}</td>
                            <td className="p-2 text-right text-gray-600">{detail.tiempo_viaje_min}</td>
                            <td className="p-2 text-center">
                              <span className="px-2 py-1 bg-green-100 text-green-700 rounded text-xs font-mono">
                                {detail.hora_llegada}
                              </span>
                            </td>
                            <td className="p-2 text-center">
                              <span className="px-2 py-1 bg-orange-100 text-orange-700 rounded text-xs font-mono">
                                {detail.hora_salida}
                              </span>
                            </td>
                            <td className="p-2 text-right text-gray-600">{detail.duracion_visita_horas}</td>
                            <td className="p-2 text-center">
                              <span className={`inline-flex items-center justify-center w-6 h-6 rounded-full text-xs font-semibold ${
                                detail.prioridad === 1 ? 'bg-red-100 text-red-700' :
                                detail.prioridad === 2 ? 'bg-yellow-100 text-yellow-700' :
                                'bg-gray-100 text-gray-700'
                              }`}>
                                {detail.prioridad}
                              </span>
                            </td>
                          </tr>
                        ))}
                      </tbody>
                      <tfoot>
                        <tr className="bg-gray-100 font-semibold">
                          <td colSpan="4" className="p-2 text-right">TOTALES:</td>
                          <td className="p-2 text-right text-blue-700">
                            {userRoute.distancia_total_km} km
                          </td>
                          <td className="p-2 text-right text-blue-700">
                            {userRoute.tiempo_total_min} min
                          </td>
                          <td colSpan="4"></td>
                        </tr>
                      </tfoot>
                    </table>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* Footer Info */}
        <div className="mt-6 bg-white rounded-lg shadow-lg p-4">
          <div className="text-sm text-gray-600">
            <p className="font-semibold text-gray-800 mb-2">Información del Algoritmo:</p>
            <ul className="space-y-1 ml-4">
              <li>• <strong>Distancia:</strong> Calculada con fórmula Haversine</li>
              <li>• <strong>Tiempo:</strong> dist_km / 40 km/h * 60 (velocidad promedio urbana)</li>
              <li>• <strong>Optimización:</strong> Nearest Neighbor + mejora 2-opt</li>
              <li>• <strong>Asignación:</strong> Distribución balanceada respetando restricciones de tiempo y visitas máximas</li>
              <li>• <strong>Prioridad:</strong> Los PDVs se ordenan por prioridad antes de asignar</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  );
};

export default RouteOptimizer
